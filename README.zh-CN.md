<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/hero.zh-CN.svg">
  <img src="docs/assets/hero.zh-CN-light.svg" alt="Seedance Movie">
</picture>

**Seedance Movie MCP。** 把故事拆成分镜，把生成片段接成视频。从故事和长文本生成分镜计划，确认后运行有界并行任务，下载片段、拼接视频，并保留供后续剪辑使用的 manifest。希望在 MCP 工作流中组织火山方舟视频生成任务的创作者与开发者。

[HRouter](https://hrouter.net/home) · [GitHub](https://github.com/honestTai/seedance-movie-mcp) · [All public projects](https://github.com/honestTai)

[English](README.md) · **简体中文**

</div>

## ❤️ 赞助商

| 赞助商 | 简介 |
| :--- | :--- |
| [![HRouter](docs/assets/hrouter-sponsor.svg)](https://hrouter.net/home) | **[HRouter](https://hrouter.net/home)**：面向 AI 编程与应用开发的模型接入、API Key 管理与用量统计，也由作者运营。 |

## 快速开始

- 如果使用系统 Node，需要 Node.js `>=20.12`。安装脚本也可以安装项目内 Node.js `v24.16.0`。
- 需要 npm。
- 需要 ffmpeg。未设置 `FFMPEG_PATH` 时，会优先使用 `ffmpeg-static`，再回退到 `PATH` 中的 `ffmpeg`。
- 需要具备视频生成权限的火山方舟 API key。

```bash
git clone https://github.com/honestTai/seedance-movie-mcp.git
cd seedance-movie-mcp
npm install
npm test
npm run build
```

在仓库外设置 API key：

```bash
export ARK_API_KEY="your-volcengine-ark-api-key"
```

```powershell
$env:ARK_API_KEY = "your-volcengine-ark-api-key"
```

运行不会调用 Ark 的 dry-run 示例：

```bash
npm run example:dry-run
```

## 功能

| 功能 | 说明 |
| :--- | :--- |
| 运行时配置 | 运行时配置来自环境变量：`ARK_API_KEY`、`ARK_MODEL`、`ARK_BASE_URL`、`ARK_MAX_CONCURRENCY`、`FFMPEG_PATH`。 |
| API key | API key 不作为工具参数传入，避免进入聊天上下文或工具日志。 |
| 提示词确认 | `generate_movie`、`generate_movie_from_text`、`generate_movie_from_scenes` 会先返回完整提示词确认计划，不会直接发起付费 Ark 请求。只有用户审阅并确认后，带回匹配的 `promptApprovalId` 才会生成。 |
| 字幕 | `subtitleMode` 支持 `none`、`manifest`、`srt`、`ass`。字幕默认是可编辑时间轴或 sidecar 文件；一键硬烧字幕已刻意禁用。 |
| 叙事 skill | 本地分镜默认采用 `witty_compact`，强调诙谐、幽默、紧凑；也支持通过 `storySkillPath` 或 `SEEDANCE_STORY_SKILL_PATH` 读取外部 Markdown/text 叙事 skill。 |
| 本地运行时 | 安装脚本可把 Node.js 安装到项目内 `.mcp-runtime`，兼容 Windows Codex App、Windows Codex CLI、Linux Codex CLI。 |
| 输出 | 返回 `sceneResults`、`parallel`、`subtitleTimeline`、`manifestPath`，方便后续交给 Codex 或视频插件继续做片头、转场、字幕包装。 |

## 文档

- [贡献指南](CONTRIBUTING.md)
- [安全策略](SECURITY.md)
- [更新日志](CHANGELOG.md)
- [skills/witty-compact.md](skills/witty-compact.md)
- [skills/cinematic-default.md](skills/cinematic-default.md)

## 许可证

MIT. See `LICENSE`. 中文非正式译文见 `LICENSE.zh-CN.md`。

## 星标历史

<a href="https://www.star-history.com/#honestTai/seedance-movie-mcp&Date">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=honestTai/seedance-movie-mcp&type=Date&theme=dark">
    <img src="https://api.star-history.com/svg?repos=honestTai/seedance-movie-mcp&type=Date" alt="seedance-movie-mcp 的 Star 历史">
  </picture>
</a>

---

<div align="center">

Made by **[honestTai](https://github.com/honestTai)** · [HRouter](https://hrouter.net/home) · [More projects](https://github.com/honestTai)

</div>
