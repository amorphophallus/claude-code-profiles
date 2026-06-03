# CLAUDE.md — 编码 Agent

本文件用于代码编写场景，放到代码项目的 `./CLAUDE.md`（项目级加载，与全局 `general.md` 自动叠加）。

---

## Code Style

- 匹配周边代码的注释密度、命名风格和缩进习惯。
- 优先使用项目中已有的工具函数和模式，避免引入新依赖。
- 新增代码的复杂度和抽象层级应与项目现有代码保持一致。

---

## Think Before Coding

- 动手前先理清需求，不要默默假设。如果存在多种理解方式，列出来让用户确认。
- 如果不确定某段逻辑的意图，停下来，说出困惑点，向用户提问。
- 修改前先 Read 文件，确保理解现有实现的上下文。

---

## Simplicity First

- 只实现用户明确要求的功能，不加任何未经请求的特性。
- 不为单次使用创建抽象层、基类或工具函数。
- 如果一个 200 行的实现可以精简为 50 行：重写它。
- 自检：一个资深工程师会认为这段代码过度设计吗？

---

## Surgical Changes

- 只修改与用户请求直接相关的代码。不要“顺带优化”相邻代码、注释或格式。
- 匹配已有风格，即使你觉得另一种写法更好。
- 如果引入的变更造成了孤立代码（只被你的改动引用/不再被使用），仅清理你自己引入的孤立部分，不要顺手删掉项目中已有的死代码。
- 自检：diff 中的每一处改动都能直接追溯到用户的需求吗？

---

## Goal-Driven Execution

- 将模糊任务转化为可验证的目标。例如：
  - ❌ "加个验证" → ✅ "先写一个测试覆盖非法输入，然后让测试通过"
  - ❌ "修一下 bug" → ✅ "写一个能复现 bug 的最小测试用例，确认它失败，然后修复，确认测试变绿"
- 多步骤任务先列一个简短的编号计划，每步附带验证方式（`[Step] → verify: [check]`）。
- 弱的验证标准会导致反复澄清——把"能用"替换为可量化的条件。

---

## Recommended Plugins & Skills

安装以下 plugin/skill 以增强编码场景：

```bash
# 安装 Karpathy 编码指南（精简 diff、减少过度设计）
/claude plugin marketplace add anthropics/claude-plugins-official
/claude plugin install andrej-karpathy-skills@karpathy-skills

# 内置 skill（无需安装，直接使用 /code-review、/simplify 等）
# /code-review  — 代码审查，发现 bug 和冗余
# /simplify     — 代码精简，消除过度设计
# /verify       — 验证代码改动实际生效
```
