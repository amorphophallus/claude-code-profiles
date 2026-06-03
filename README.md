# Claude Code Profiles

可组合的 Claude Code 配置方案——为不同工作场景切换最佳 Agent 行为。

## 安装

```bash
git clone git@github.com:amorphophallus/claude-code-profiles.git ~/claude-code-profiles

# 部署全局基础规则（所有场景生效）
ln -sf ~/claude-code-profiles/general.md ~/.claude/CLAUDE.md
```

## 使用方法

Claude Code 的 CLAUDE.md 有两层加载机制：

| 层级 | 路径 | 作用 |
|---|---|---|
| 全局 | `~/.claude/CLAUDE.md` | 所有项目始终加载 |
| 项目 | `./CLAUDE.md` | 仅在当前项目目录加载 |

**组合方式**：`全局基础规则（general.md）+ 项目场景规则（X.md）= 最终行为**。

```bash
# 编码项目
ln -sf ~/claude-code-profiles/code.md ./CLAUDE.md

# 调研项目
ln -sf ~/claude-code-profiles/research.md ./CLAUDE.md

# 训练项目
ln -sf ~/claude-code-profiles/experiment.md ./CLAUDE.md
```

## 场景说明

### `general.md` — 全局基础（必装）

所有 Agent 共享的基础规则：
- Git 提交规范（conventional commits、提交前检查）
- 通用工作流（副作用确认、工具使用、memory 管理）
- 长时间任务时间感知与进度汇报

### `code.md` — 编码 Agent

适合需要写代码、改代码、代码审查的场景。

**规则覆盖**：
- 代码风格匹配（注释密度、命名、缩进）
- Think Before Coding（理解需求再动手）
- Simplicity First（极简实现，不过度设计）
- Surgical Changes（精准修改，不改无关代码）
- Goal-Driven Execution（目标驱动，可验证结果）

**推荐安装**：
```bash
# Karpathy 编码指南插件（减少无关 diff、避免过度设计）
/claude plugin marketplace add anthropics/claude-plugins-official
/claude plugin install andrej-karpathy-skills@karpathy-skills

# 内置 skill（直接使用）：
# /code-review  — 代码审查，发现 bug 和冗余
# /simplify     — 代码精简
# /verify       — 验证代码改动
```

### `research.md` — 科研调研 Agent

适合文献调研、信息检索、论文阅读场景。

**规则覆盖**：
- 多源交叉验证（关键事实至少 2 个独立来源）
- 深度调研流程（广度扫→深度挖→补缺）
- 结论引用格式（附来源、区分事实与判断）
- 论文阅读方法（IMRaD 提取、数字校验、局限总结）

**推荐使用**：
```bash
# 内置 skill（直接使用）：
# /deep-research — 多源深度调研，自动 fan-out 搜索并交叉验证
```

### `experiment.md` — 实验训练 Agent

适合跑深度学习实验、GPU 训练、批量任务场景。

**规则覆盖**：
- GPU 资源管理（显存确认、多卡验证、断点恢复）
- 实验记录与可复现（超参、种子、数据版本、结果归档）
- 长时间任务强化（批量实验进度、loss 趋势、异常告警）

**推荐使用**：
```bash
# 内置 skill：
# /loop   — 定时轮询训练日志、GPU 状态
# /verify — 验证实验脚本改动
```

## 自定义

1. Fork 本仓库
2. 修改或新增对应的 `.md` 文件
3. 在新机器上 `git clone` + `ln -sf` 即可

## License

MIT
