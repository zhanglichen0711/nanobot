# nanobot 中文改造版 · DeepSeek 接入快速上手

> **本仓库来源**：Fork 自 [HKUDS/nanobot](https://github.com/HKUDS/nanobot)（commit `66f5f2d`，2026-09-29）。
> nanobot 是一个超轻量的自托管个人 AI Agent 框架（Python）：WebUI / 终端 / 聊天应用三种入口，
> 内置工具调用、长期记忆、MCP、多 Agent 委派、定时任务和 OpenAI 兼容 API，核心代码小而可读。
> **MIT License 原样保留（见 [LICENSE](LICENSE)），版权归 Xubin Ren 及 nanobot 贡献者所有**，本仓库改动同样以 MIT 发布。
> 原版 README 见 [README_UPSTREAM.md](README_UPSTREAM.md)，完整文档见 [docs/](docs/)。

## 本仓库改了什么

轻改造，核心代码一行没动：

| 改动 | 说明 |
|---|---|
| 新增 `README.md`（本文件） | 中文上手指南，重点是接 DeepSeek 的最小可用配置 |
| 新增 `config.deepseek.example.json` | 可直接合并进 `~/.nanobot/config.json` 的 provider 配置 |
| 原 `README.md` 重命名为 `README_UPSTREAM.md` | 内容未动 |

适合谁：想要一个**能自己部署、核心能读完、模型随便换**的个人 Agent，
LLM 走 DeepSeek API（或任何 OpenAI 兼容端点，包括你本地 vLLM 起的服务）。

## 为什么是 nanobot

市面上的 Agent 框架两极分化：要么是重平台（装完一套 Docker 还没开始写逻辑），
要么是玩具 demo。nanobot 卡在中间：单机 `pip install` 就能跑，
工具/记忆/MCP/多 Agent 都是同一套小核心里长出来的，出了问题能顺着代码读下去——
这对「想搞懂 Agent 内部机制」的人比黑盒平台有价值得多。

## 快速上手

### 1. 安装

```bash
# 方式一：PyPI 安装
pip install nanobot-ai

# 方式二：源码安装（本仓库）
pip install -e .

# 首次初始化（生成 ~/.nanobot/config.json）
nanobot onboard
```

### 2. 接入 DeepSeek

先把 Key 放进环境变量（别写进配置文件）：

```powershell
# Windows PowerShell
$env:DEEPSEEK_API_KEY = "sk-你的Key"
```

```bash
# macOS / Linux
export DEEPSEEK_API_KEY="sk-你的Key"
```

然后把 `config.deepseek.example.json` 的内容合并进 `~/.nanobot/config.json`，
关键就是三段：`providers` 指向 DeepSeek 的 OpenAI 兼容端点，`modelPresets` 起个名字，
`agents.defaults.modelPreset` 引用这个名字。

### 3. 验证并启动

```bash
nanobot status              # 看模型是否就绪
nanobot agent -m "你好，介绍一下你自己"   # CLI 先跑通
nanobot webui               # 打开 http://127.0.0.1:8765 的 WebUI
```

CLI 通了再接 WebUI / 聊天应用。想在 WebUI 里点选配置的话，
走 `Settings → Models`，效果等价于手编 JSON。

## DeepSeek 配置说明（详见 `config.deepseek.example.json`）

| 字段 | 值 | 说明 |
|---|---|---|
| `providers.deepseek.apiBase` | `https://api.deepseek.com/v1` | OpenAI 兼容端点，带 `/v1` |
| `providers.deepseek.apiKey` | `${DEEPSEEK_API_KEY}` | 引用环境变量，Key 不落盘 |
| `modelPresets.primary.model` | `deepseek-chat` | 也可换 `deepseek-reasoner` |
| `contextWindowTokens` | `131072` | 按所用模型实际上下文调整 |

本地模型同样套路：`apiBase` 换成 `http://127.0.0.1:8000/v1`（vLLM）或
`http://localhost:11434/v1`（Ollama），`apiKey` 用占位符 `EMPTY` 即可。

## 踩坑提示

1. **`nanobot agent -m` 先于 WebUI**——CLI 报错信息最直接，WebUI 不通时先用 CLI 定位是模型还是通道问题。
2. **环境变量只在当前终端有效**——长期跑 gateway 记得在服务环境里设 `DEEPSEEK_API_KEY`。
3. **多 Agent / MCP 配置改动后跑 `nanobot status`** 验证，不要凭感觉认为生效。

## 同步上游

```bash
git remote add upstream https://github.com/HKUDS/nanobot.git
git fetch upstream main --depth 1
git merge FETCH_HEAD   # 冲突优先保留上游，本仓库改动只集中在 README 和配置模板
```

## 相关

- 原项目：https://github.com/HKUDS/nanobot （给原作者点 star）
- 文档：`docs/configuration.md`、`docs/provider-cookbook.md`、`docs/providers.md`
- 我的博客（RAG / Agent 工程实践系列）：https://lczhang.blog.cn
