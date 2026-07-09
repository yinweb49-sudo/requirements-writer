# requirements-writer

一个**与项目解耦**的 Agent Skill：用固定、可复用的结构编写软件功能的开发需求文档（PRD / 技术需求说明）与分期开发提示词。

先固定业务目标、产品决策和边界，再写架构、交互、数据、接口、复用 / 清理清单，最后拆成可独立执行和验收的开发期次。方法论通用；项目相关的架构名、数据库、鉴权、验收方式、样本文档全部从**项目档案**读取，所以同一个 skill 能用在任意项目。

## 目录结构

```text
requirements-writer/
├── SKILL.md                              # skill 主文件（方法论 + 工作流，含“第0步读项目档案”）
├── agents/
│   └── openai.yaml                       # 兼容 openai 风格 agent 的接口描述
├── references/
│   ├── requirements-doc-template.md      # 主需求文档模板（11 节，带占位符）
│   ├── phase-prompt-template.md          # 分期开发提示词模板
│   ├── review-checklist.md               # 需求 / 提示词评审清单
│   └── project-profile-template.md       # 【新项目填这个】项目档案空白模板
└── examples/
    └── example-profile.md                # 一份填好的示例项目档案（虚构，供参考格式）
```

## 核心设计：通用引擎 + 项目档案

- **通用引擎**（本仓库全部内容）：方法论和模板，不含任何具体项目的路径、术语、端口。
- **项目档案**（`requirements-profile.md`，留在你自己的项目里）：一次性声明本项目的架构分层、数据库、鉴权 / 白名单、验收方式、结构检索工具、样本文档路径。

skill 运行时先读项目档案，把模板里的占位符（`{{前端}}`、`{{本地后端}}`、`{{云端后端}}`、`{{主库}}`、`{{次库}}`、`{{鉴权方案}}`、`{{白名单机制}}`、`{{验收方式}}`、`{{结构检索工具}}`）替换成真实值。**项目里不存在的层 / 库 / 机制会被自动裁剪掉，不会硬套三层架构。**

## 安装

skill 的加载目录取决于你的 Agent 工具（Claude Code / Codex 等）。常见三种方式：

### 方式 A · 用户级全局（自用多项目最省事）

放到用户级 skills 目录，所有项目自动可见：

```bash
git clone https://github.com/yinweb49-sudo/requirements-writer.git \
  ~/.claude/skills/requirements-writer
```

> Windows PowerShell：`git clone <url> "$env:USERPROFILE\.claude\skills\requirements-writer"`
> 有的工具用 `.agents/skills/` 或 `.codex/skills/`，按你的工具约定放。

### 方式 B · 作为项目子模块（团队 / 多机、版本可控）

```bash
git submodule add https://github.com/yinweb49-sudo/requirements-writer.git \
  .claude/skills/requirements-writer
git pull --recurse-submodules   # 后续拉更新
```

### 方式 C · 下载压缩包解压（一键装，不用 git）

从仓库 Releases 或 `Code → Download ZIP` 下载，解压到 skills 目录即可。

## 使用（三步）

1. **建项目档案**：复制 `references/project-profile-template.md` 到你的项目仓库根（或 `.agents/`），改名为 `requirements-profile.md`，按提示填空。可参考 `examples/example-profile.md`（虚构示例）。
2. **触发 skill**：让 AI“按 requirements-writer 编写 XX 功能的开发需求文档 / 分期开发提示词”。
3. skill 自动读档案 → 用真实术语套模板 → 产出主需求文档 + `开发提示词/期N-*.md`。

> 项目没建档案也能用：skill 会退而读 `CLAUDE.md` / `README.md`，再不行就问你几个关键问题。建档案只是让它一次到位、不用每次追问。

## 许可

MIT License，见 `LICENSE`。
