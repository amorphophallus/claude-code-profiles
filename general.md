# CLAUDE.md — 全局基础规则

本文件应用于所有场景，部署到 `~/.claude/CLAUDE.md`（全局加载）。

---

## Git Commit Conventions

- **禁止在 commit message 中追加 `Co-Authored-By:` 署名行。** 不要写任何形式的 co-author 标记（包括 `Co-Authored-By:`, `Co-authored-by:`, `Signed-off-by:` 等变体）。
- Commit message 使用英文小写开头，格式遵循 conventional commits 风格（`feat:`, `fix:`, `docs:`, `refactor:`, `chore:` 等）。
- 如有 body，与 subject 之间空一行，body 使用 bullet list（`-`）。

### 提交前检查

- 确认文件变更范围和 diff 内容
- 给用户 commit message 建议，确认后再执行 `git commit`
- Push 前确认远端分支

---

## General Workflow

- 执行有副作用的操作（删除文件、覆盖远端、修改生产配置）前，先确认
- 读取文件用 Read 工具，编辑用 Edit/Write 工具，避免用 `cat`/`sed`/`echo` 等 shell 命令操作文件内容
- 如果用户要求记住某事，写入 memory（`~/.claude/projects/.../memory/`），并在 `MEMORY.md` 中建立索引

---

## 长时间任务的时间感知与进度汇报

在执行任何命令或任务之前，先估算其预计完成时间。如果预计超过 **10 分钟**（例如批量运行实验、多轮训练、大量 web search 调研等场景），必须遵循以下规则：

### 启动前

- 在开始执行之前，明确告知用户预计总耗时，让用户有心理预期。

### 执行中：每 10 分钟汇报一次进度

每间隔约 10 分钟，主动做一次进度汇报。汇报内容必须包含以下三项：

1. **当前在做什么** — 展示部分任务输出或当前阶段描述，让用户知道任务没有卡死。
2. **已执行时长** — 从任务启动到现在的实际耗时。
3. **预计剩余时间（最重要）** — 基于当前进展实时重新评估，给出更新后的预计剩余时间。如果发现耗时远超初始预估，如实说明原因并给出新的完成时间。

### 汇报风格

- 简洁直接，用 bullet list 即可。
- 如果任务提前完成或中途失败，立即告知，不要等到下一个 10 分钟节点。
- 对于嵌套或并行任务（多个 agent 同时工作），汇报时说明各路的进展概况。
