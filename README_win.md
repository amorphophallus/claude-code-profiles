# Claude Code Profiles（Windows）

可组合的 Claude Code 配置方案——为不同工作场景切换最佳 Agent 行为。

以下命令默认使用 **PowerShell**，标注 `cmd` 的用命令提示符。

## 安装

```powershell
git clone git@github.com:amorphophallus/claude-code-profiles.git $env:USERPROFILE\claude-code-profiles
```

Claude Code 的 CLAUDE.md 有两层加载机制：

| 层级 | 路径 | 作用 |
|---|---|---|
| 全局 | `%USERPROFILE%\.claude\CLAUDE.md` | 所有项目始终加载 |
| 项目 | `.\CLAUDE.md` | 仅在当前项目目录加载 |

### 完整安装

全部 3 个 profile（general + code + experiment）合并到一个 CLAUDE.md。research 场景的能力由配套 skill 与 plugin 提供，详见下方安装步骤。

#### 安装到全局

```powershell
# PowerShell
Get-Content .\general.md, `
            .\code.md, `
            .\experiment.md `
    | Out-File -Append -Encoding UTF8 $env:USERPROFILE\.claude\CLAUDE.md
```

```cmd
REM cmd
type .\general.md ^
     .\code.md ^
     .\experiment.md ^
     >> %USERPROFILE%\.claude\CLAUDE.md
```

#### 安装到项目

```powershell
# PowerShell（在项目目录下执行）
Get-Content $env:USERPROFILE\claude-code-profiles\general.md, `
            $env:USERPROFILE\claude-code-profiles\code.md, `
            $env:USERPROFILE\claude-code-profiles\experiment.md `
    | Out-File -Append -Encoding UTF8 .\CLAUDE.md
```

```cmd
REM cmd（在项目目录下执行）
type %USERPROFILE%\claude-code-profiles\general.md ^
     %USERPROFILE%\claude-code-profiles\code.md ^
     %USERPROFILE%\claude-code-profiles\experiment.md ^
     >> .\CLAUDE.md
```

#### 安装配套 skill 与 plugin

research 场景的能力由两组外部 skill 提供，不通过 CLAUDE.md profile 加载：

| 来源 | 用途 | 安装方式 |
|---|---|---|
| [nature-skills](https://github.com/Yuan1z0825/nature-skills) | 学术写作、润色、引用、图表、投稿回复 | Claude Code plugin |
| [dailypaper-skills](https://github.com/huangkiki/dailypaper-skills) | 每日论文推荐、论文阅读、笔记生成 | 用户 skill 目录 |

**nature-skills（plugin 安装）**

在 `%USERPROFILE%\.claude\settings.json` 中添加 marketplace 并启用插件：

```json
{
  "extraKnownMarketplaces": {
    "nature-skills": {
      "source": {
        "source": "git",
        "url": "https://github.com/Yuan1z0825/nature-skills.git"
      }
    }
  },
  "enabledPlugins": {
    "nature-skills@nature-skills": true
  }
}
```

或通过 CLI 安装：

```powershell
claude --prompt "/plugin install nature-skills@nature-skills"
```

**dailypaper-skills（用户 skill 安装）**

```powershell
# 1. 克隆仓库
git clone https://github.com/huangkiki/dailypaper-skills.git $env:TEMP\dailypaper-skills

# 2. 安装 skill 到用户目录
New-Item -ItemType Directory -Force -Path $env:USERPROFILE\.claude\skills
Copy-Item -Recurse -Force $env:TEMP\dailypaper-skills\skills\* $env:USERPROFILE\.claude\skills\

# 3. 编辑 %USERPROFILE%\.claude\skills\_shared\user-config.json，设置：
#    - obsidian_vault: Obsidian vault 路径
#    - keywords: 论文过滤关键词

# 4. 准备 Obsidian 目录
$VAULT = "$env:USERPROFILE\Documents\ObsidianVault"
New-Item -ItemType Directory -Force -Path "$VAULT\DailyPapers"
New-Item -ItemType Directory -Force -Path "$VAULT\论文笔记\_概念\0-待分类"
New-Item -ItemType Directory -Force -Path "$VAULT\论文笔记\_待整理"
```

```cmd
REM cmd
git clone https://github.com/huangkiki/dailypaper-skills.git %TEMP%\dailypaper-skills
mkdir %USERPROFILE%\.claude\skills 2>nul
xcopy /E /I /Y %TEMP%\dailypaper-skills\skills\* %USERPROFILE%\.claude\skills\

REM 编辑 %USERPROFILE%\.claude\skills\_shared\user-config.json

REM 准备 Obsidian 目录
set VAULT=%USERPROFILE%\Documents\ObsidianVault
mkdir "%VAULT%\DailyPapers" 2>nul
mkdir "%VAULT%\论文笔记\_概念\0-待分类" 2>nul
mkdir "%VAULT%\论文笔记\_待整理" 2>nul
```

**前置依赖**

```powershell
# Poppler（Windows 无包管理器，手动下载）
# 1. 打开 https://github.com/oschwartz10612/poppler-windows/releases
# 2. 下载最新 poppler-xx.x.x.zip
# 3. 解压到如 C:\Tools\poppler
# 4. 将 C:\Tools\poppler\Library\bin 加入 PATH 环境变量

# 或使用 Chocolatey
choco install poppler

# Python 依赖
pip install arxiv pyzotero pdfplumber
```

### 部分安装

根据工作场景选择需要的 profile 组合。

#### 安装到全局

```powershell
# PowerShell
# 基础用法：general + code（最常用）
Get-Content $env:USERPROFILE\claude-code-profiles\general.md, `
            $env:USERPROFILE\claude-code-profiles\code.md `
    | Out-File -Append -Encoding UTF8 $env:USERPROFILE\.claude\CLAUDE.md

# 基础用法 + 实验
Get-Content $env:USERPROFILE\claude-code-profiles\general.md, `
            $env:USERPROFILE\claude-code-profiles\code.md, `
            $env:USERPROFILE\claude-code-profiles\experiment.md `
    | Out-File -Append -Encoding UTF8 $env:USERPROFILE\.claude\CLAUDE.md
```

```cmd
REM cmd
REM 基础用法：general + code（最常用）
type %USERPROFILE%\claude-code-profiles\general.md ^
     %USERPROFILE%\claude-code-profiles\code.md ^
     >> %USERPROFILE%\.claude\CLAUDE.md

REM 基础用法 + 实验
type %USERPROFILE%\claude-code-profiles\general.md ^
     %USERPROFILE%\claude-code-profiles\code.md ^
     %USERPROFILE%\claude-code-profiles\experiment.md ^
     >> %USERPROFILE%\.claude\CLAUDE.md
```

#### 安装到项目

```powershell
# PowerShell（在项目目录下执行）
# 编码项目
Get-Content $env:USERPROFILE\claude-code-profiles\general.md, `
            $env:USERPROFILE\claude-code-profiles\code.md `
    | Out-File -Append -Encoding UTF8 .\CLAUDE.md

# 训练项目
Get-Content $env:USERPROFILE\claude-code-profiles\general.md, `
            $env:USERPROFILE\claude-code-profiles\experiment.md `
    | Out-File -Append -Encoding UTF8 .\CLAUDE.md
```

```cmd
REM cmd（在项目目录下执行）
REM 编码项目
type %USERPROFILE%\claude-code-profiles\general.md ^
     %USERPROFILE%\claude-code-profiles\code.md ^
     >> .\CLAUDE.md

REM 训练项目
type %USERPROFILE%\claude-code-profiles\general.md ^
     %USERPROFILE%\claude-code-profiles\experiment.md ^
     >> .\CLAUDE.md
```

> **注意**：如果目标 CLAUDE.md 已有内容，`>>` / `-Append` 会追加到末尾。如需替换已有内容，先用 `Out-File`（无 `-Append`）或 `>` 覆盖，后续再用追加模式。

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

### 科研调研（skill + plugin，无独立 profile）

科研场景的能力不由 CLAUDE.md profile 提供，而是通过两组外部 skill 实现（安装步骤见上方[完整安装 → 安装配套 skill 与 plugin](#安装配套-skill-与-plugin)）：

| 来源 | 用途 |
|---|---|
| [nature-skills](https://github.com/Yuan1z0825/nature-skills) | 学术写作、润色、引用、图表、投稿回复（9 个 skill） |
| [dailypaper-skills](https://github.com/huangkiki/dailypaper-skills) | 每日论文推荐、论文阅读、笔记生成（7 个 skill） |

日常使用时，在 Claude Code 会话中直接触发对应的 skill 即可（如「今日论文推荐」、`/nature-polishing` 等）。

### `experiment.md` — 实验训练 Agent

适合跑深度学习实验、GPU 训练、批量任务场景。

**规则覆盖**：
- GPU 资源管理（显存确认、多卡验证、断点恢复）
- 实验记录与可复现（超参、种子、数据版本、结果归档）
- 长时间任务强化（批量实验进度、loss 趋势、异常告警）

#### for robust-rearrangement

在 rr 项目中执行训练或 eval 任务时，建议将以下两份操作手册加入 context：

```powershell
# PowerShell（在 rr 项目目录下执行）
# 训练任务：批量实验超参对照表 + 服务器磁盘分布 + 调度流程
Get-Content $env:USERPROFILE\projects\robust-rearrangement-custom\reports\claude\batch_train_guide.md `
    | Out-File -Append -Encoding UTF8 .\CLAUDE.md

# Eval 任务：eval 参数对照表 + 结果收集模板 + 磁盘清理策略
Get-Content $env:USERPROFILE\projects\robust-rearrangement-custom\reports\claude\batch_eval_guide.md `
    | Out-File -Append -Encoding UTF8 .\CLAUDE.md
```

> 这两份 guide 包含该项目的具体超参、服务器 IP、数据分布等敏感信息，不放入本公共仓库。使用方式是在项目 `.\CLAUDE.md` 中 append 进去。

## PowerShell vs cmd 速查

| 操作 | PowerShell | cmd |
|---|---|---|
| 合并文件追加 | `Get-Content a.md,b.md \| Out-File -Append t.md` | `type a.md b.md >> t.md` |
| 创建目录 | `New-Item -ItemType Directory -Force -Path p` | `mkdir p` |
| 复制目录 | `Copy-Item -Recurse -Force src\* dst\` | `xcopy /E /I /Y src\* dst\` |
| 用户目录 | `$env:USERPROFILE` | `%USERPROFILE%` |
| 临时目录 | `$env:TEMP` | `%TEMP%` |
| 当前项目文件 | `.\CLAUDE.md` | `.\CLAUDE.md` |
| 续行符 | `` ` `` (反引号) | `^` (脱字符) |

## 自定义

1. Fork 本仓库
2. 修改或新增对应的 `.md` 文件
3. 新机器上 `git clone` 后按上述安装方法追加到对应 CLAUDE.md 即可

## License

MIT
