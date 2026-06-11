# Claude Code Profiles

可组合的 Claude Code 配置方案——为不同工作场景切换最佳 Agent 行为。

## 安装

```bash
git clone git@github.com:amorphophallus/claude-code-profiles.git ~/claude-code-profiles
```

Claude Code 的 CLAUDE.md 有两层加载机制：

| 层级 | 路径 | 作用 |
|---|---|---|
| 全局 | `~/.claude/CLAUDE.md` | 所有项目始终加载 |
| 项目 | `./CLAUDE.md` | 仅在当前项目目录加载 |

### 完整安装

全部 4 个 profile（general + code + research + experiment）合并到一个 CLAUDE.md。

#### 安装到全局

```bash
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/code.md \
    ~/claude-code-profiles/research.md \
    ~/claude-code-profiles/experiment.md \
    >> ~/.claude/CLAUDE.md
```

#### 安装到项目

```bash
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/code.md \
    ~/claude-code-profiles/research.md \
    ~/claude-code-profiles/experiment.md \
    >> ./CLAUDE.md
```

### 部分安装

根据工作场景选择需要的 profile 组合。

#### 安装到全局

```bash
# 基础用法：general + code（最常用）
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/code.md \
    >> ~/.claude/CLAUDE.md

# 基础用法 + 调研
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/code.md \
    ~/claude-code-profiles/research.md \
    >> ~/.claude/CLAUDE.md

# 基础用法 + 实验
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/code.md \
    ~/claude-code-profiles/experiment.md \
    >> ~/.claude/CLAUDE.md
```

#### 安装到项目

```bash
# 编码项目
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/code.md \
    >> ./CLAUDE.md

# 调研项目
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/research.md \
    >> ./CLAUDE.md

# 训练项目
cat ~/claude-code-profiles/general.md \
    ~/claude-code-profiles/experiment.md \
    >> ./CLAUDE.md
```

> **注意**：如果目标 CLAUDE.md 已有内容，`>>` 会追加到末尾。如需替换已有内容，先用 `> ./CLAUDE.md` 覆盖，后续用 `>>` 追加。

## 场景说明

### `general.md` — 全局基础（必装）

所有 Agent 共享的基础规则：
- Git 提交规范（conventional commits、提交前检查）
- 通用工作流（副作用确认、工具使用、memory 管理）
- 长时间任务时间感知与进度汇报

### `code.md` — 编码 Agent

适合需要写代码、改代码、代码审查的场景。from karpathy-skills.

**规则覆盖**：
- 代码风格匹配（注释密度、命名、缩进）
- Think Before Coding（理解需求再动手）
- Simplicity First（极简实现，不过度设计）
- Surgical Changes（精准修改，不改无关代码）
- Goal-Driven Execution（目标驱动，可验证结果）

### `research.md` — 科研调研 Agent

TBD

### `experiment.md` — 实验训练 Agent

适合跑深度学习实验、GPU 训练、批量任务场景。

**规则覆盖**：
- GPU 资源管理（显存确认、多卡验证、断点恢复）
- 实验记录与可复现（超参、种子、数据版本、结果归档）
- 长时间任务强化（批量实验进度、loss 趋势、异常告警）

#### for robust-rearrangement

在 rr 项目中执行训练或 eval 任务时，建议将以下两份操作手册加入 context：

```bash
# 训练任务：批量实验超参对照表 + 服务器磁盘分布 + 调度流程
cat ~/projects/robust-rearrangement-custom/reports/claude/batch_train_guide.md >> ./CLAUDE.md

# Eval 任务：eval 参数对照表 + 结果收集模板 + 磁盘清理策略
cat ~/projects/robust-rearrangement-custom/reports/claude/batch_eval_guide.md >> ./CLAUDE.md
```

> 这两份 guide 包含该项目的具体超参、服务器 IP、数据分布等敏感信息，不放入本公共仓库。使用方式是在项目 `./CLAUDE.md` 中 append 进去。

## 自定义

1. Fork 本仓库
2. 修改或新增对应的 `.md` 文件
3. 新机器上 `git clone` 后按上述安装方法 `cat >>` 到对应 CLAUDE.md 即可

## License

MIT
