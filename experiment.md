# CLAUDE.md — 实验训练 Agent



## 实验前：资源摸底与参数登记

### 服务器资源审计

启动任何实验前，先对全部目标服务器做资源摸底：
- GPU 空闲情况（每台服务器有几张空闲卡）
- 磁盘空间（`df -h`，预留每实验 ~150G 数据 + checkpoint）
- **磁盘速度分级**：NVMe（>1GB/s 随机读）> SATA SSD > HDD。训练数据绝对禁止放在 HDD 上——IO 竞争会导致同服所有训练降速。

### 超参对照表

每次批量实验维护一张超参对照表（markdown 表格），明确标注：
- 所有实验的**共同固定参数**（batch_size、epochs、wandb project 等）
- 每个实验的**差异参数**（消融变量）
- 对应的脚本变量设置（方便直接复制到启动脚本）

### 数据准备

- 训练前验证目标服务器有所需数据：`ssh <host> "ls <data_path>/<suffix>*.lmdb"`
- 数据不存在时：优先从 SSD/NVMe 源 SCP，避免从 HDD 源传输（>3h/100G）
- 数据传输后立即验证完整性（`du -sh` + `ls data.mdb`）
- 记录数据分布表（哪个服务器有哪些数据集），后续实验复用

---

## 实验调度：紧循环模型

核心原则：**不在实验之间插入不必要的等待**。找到空闲 GPU 就立即启动下一个实验。

```
待跑队列按优先级排序
while 有待跑实验:
    1. 检查所有服务器 GPU 状态 → 找有空闲卡的
    2. 如有空闲: 立即从队列取下一个 pending 实验启动
    3. 如无空闲: 等待已有实验释放 GPU，sleep 后重试
    4. 启动后立即登记: 服务器、tmux session、wandb project/run_name
```

- 同服务器多实验并行时，显式指定 `GPU_ID` 避让已有实验
- 优先将实验分配到已有对应数据的服务器（减少 SCP）

---

## 训练监控：分级策略

### 第 1 小时（密集监控）

每 10 分钟检查一次：
- Crash 信号（Traceback / Error / Killed / OOM）
- 训练速度是否正常（对比 baseline 的 it/s）
- wandb 同步状态

```bash
ssh <host> "tmux capture-pane -pt <session>:train -S -30" | \
  grep -E "Traceback|Error|Killed|OOM|it/s|epoch|CheckpointSaver"
```

### 1 小时后（稳定期）

如果训练稳定（无 crash、速度正常、loss 正常下降），降频到每 6 小时检查。

### 异常处理速查

| 异常 | 处理 |
|------|------|
| 速度显著慢 | 检查数据盘是否为 HDD，迁移到 SSD |
| 磁盘满 | 清理非保护数据（pip/torch cache、旧 rollout pkl） |
| tmux 僵死 | `tmux send-keys -t <session>:train C-c` 安全终止 |

---

## 中断恢复

### 中断分类

| 类型 | 处理 |
|------|------|
| OOM / 磁盘满 | 清理资源后重启 |
| NAS 挂死（进程 D-state） | 等 NAS 恢复，清理僵尸进程后重启 |
| 服务器宕机 | 等恢复后在原服务器或换服务器 resume |
| GPU 被抢占 | 找其他空闲 GPU 继续 |

### Resume 流程

1. 记录中断信息（实验编号、wandb run_id、中断原因、当前 epoch）
2. 清理残留：`tmux kill-session -t <session>`，确认 GPU 释放
3. 检查 checkpoint 存在（NAS 或本地路径，确认最新 `*.pt`）
4. 修改启动脚本：设置 `WANDB_CONTINUE_RUN_ID="<run_id>"`（注意：是 run_id 如 `rx6nfry4`，不是 run_name）（注意检查代码中的具体实现，是否是 wandb.continue_run_id 这个参数。如果没有该功能存在，提示用户实现该功能）
5. 启动后验证：起始 epoch > 0，wandb 状态恢复为 running

### 安全终止进程

- ✅ **首选**：`tmux send-keys -t <session>:train C-c` — 只杀目标 session，天然隔离
- ✅ **次选**：`pkill -f 'continue_run_id=<exact_id>'` — 按 run_id 精确匹配
- ❌ **禁止**：`pkill -9 -u <user>` — 会误杀同服务器所有实验
- ❌ **禁止**：批量 `kill -9` GPU 上所有 PID — 会误杀他人进程

---

## 数据传输

- **首选**：服务器间直传 `ssh <target> "scp -r <src>:/path <dst>"` — ~80-100 MB/s (SSD 源)
- **避免**：`scp -3` 本机中转 (~2-3 MB/s)、tar pipe 中转 (<1 MB/s)
- **HDD 源**：仅作为最后手段（~3-5 MB/s），标注风险
- 传输完成后验证 `data.mdb` 存在 + 大小一致

---

## Recommended Skills

```bash
# 内置 skill:
# /loop   — 定时轮询训练日志、GPU 状态
# /verify — 验证实验脚本改动
```
