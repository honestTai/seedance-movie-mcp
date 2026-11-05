<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/hero.svg">
  <img src="docs/assets/hero-light.svg" alt="Seedance Movie">
</picture>

**Seedance Movie MCP.** Turn stories into scenes, and generated clips into video. Plan scenes from a story, approve the prompts, run bounded parallel tasks, download and assemble clips, and keep a manifest for further editing. Creators and developers orchestrating Volcengine Ark video generation through MCP.

[HRouter](https://hrouter.net/home) · [GitHub](https://github.com/honestTai/seedance-movie-mcp) · [All public projects](https://github.com/honestTai)

**English** · [简体中文](README.zh-CN.md)

</div>

## ❤️ Sponsor

| Sponsor | What it is |
| :--- | :--- |
| [![HRouter](docs/assets/hrouter-sponsor.svg)](https://hrouter.net/home) | **[HRouter](https://hrouter.net/home)**: model access, API key management, and usage tracking for AI coding and application development. Also operated by the maintainer. |

## Quick start

- Node.js `>=20.12` if you run with your system Node. The installer can also install project-local Node.js `v24.16.0`.
- npm.
- ffmpeg. If `FFMPEG_PATH` is not set, the server tries `ffmpeg-static`, then falls back to `ffmpeg` from `PATH`.
- A Volcengine Ark API key with video generation access.

```bash
git clone https://github.com/honestTai/seedance-movie-mcp.git
cd seedance-movie-mcp
npm install
npm test
npm run build
```

Set your API key outside the repository:

```bash
export ARK_API_KEY="your-volcengine-ark-api-key"
```

```powershell
$env:ARK_API_KEY = "your-volcengine-ark-api-key"
```

Run a dry-run example that does not call Ark:

```bash
npm run example:dry-run
```

## Features

| Feature | What it does |
| :--- | :--- |
| Runtime config | Runtime config comes from environment variables: `ARK_API_KEY`, `ARK_MODEL`, `ARK_BASE_URL`, `ARK_MAX_CONCURRENCY`, `FFMPEG_PATH`. |
| API keys | API keys are not tool parameters, so they do not need to enter chat context. |
| Prompt approval | `generate_movie`, `generate_movie_from_text`, and `generate_movie_from_scenes` return a full prompt approval plan before any paid Ark request. Re-run with the matching `promptApprovalId` only after the user reviews and approves the prompts. |
| Subtitles | `subtitleMode` supports `none`, `manifest`, `srt`, and `ass`. Subtitles are editable timeline or sidecar artifacts; one-shot burn-in is intentionally not supported. |
| Story skills | Story splitting defaults to `witty_compact`, a humorous and tight narrative style. You can also pass a local Markdown/text story skill with `storySkillPath` or `SEEDANCE_STORY_SKILL_PATH`. |
| Local runtime | A project-local Node.js runtime can be installed into `.mcp-runtime` so Codex Desktop, Codex CLI on Windows, and Codex CLI on Linux do not depend on the system Node version. |
| Outputs | The server outputs `sceneResults`, `parallel`, `subtitleTimeline`, and `manifestPath` so later editing tools can package clips, titles, transitions, captions, and subtitles. |

## Docs

- [Contributing / 贡献指南](CONTRIBUTING.md)
- [Security Policy / 安全策略](SECURITY.md)
- [Changelog / 更新日志](CHANGELOG.md)
- [skills/witty-compact.md](skills/witty-compact.md)
- [skills/cinematic-default.md](skills/cinematic-default.md)

## License

MIT. See [LICENSE](LICENSE). A non-authoritative Chinese translation is available in [LICENSE.zh-CN.md](LICENSE.zh-CN.md).

## Star history

<a href="https://www.star-history.com/#honestTai/seedance-movie-mcp&Date">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=honestTai/seedance-movie-mcp&type=Date&theme=dark">
    <img src="https://api.star-history.com/svg?repos=honestTai/seedance-movie-mcp&type=Date" alt="Star history of seedance-movie-mcp">
  </picture>
</a>

---

<div align="center">

Made by **[honestTai](https://github.com/honestTai)** · [HRouter](https://hrouter.net/home) · [More projects](https://github.com/honestTai)

</div>
