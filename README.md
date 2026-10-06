<div align="center">

**English** · [简体中文](README.zh-CN.md)

[HRouter](https://hrouter.net/home) · [All public projects](https://github.com/honestTai) · [Star & Fork trends](#project-activity)

</div>

[![Repository summary](https://raw.githubusercontent.com/honestTai/honestTai/main/assets/badges/seedance-movie-mcp.svg)](#project-activity)

<div align="center">

# Seedance Movie MCP

**Turn stories into scenes, and generated clips into video.**

</div>

Plan scenes from a story, approve the prompts, run bounded parallel tasks, download and assemble clips, and keep a manifest for further editing.

Creators and developers orchestrating Volcengine Ark video generation through MCP.

## Highlights

- Runtime config comes from environment variables: `ARK_API_KEY`, `ARK_MODEL`, `ARK_BASE_URL`, `ARK_MAX_CONCURRENCY`, `FFMPEG_PATH`.
- API keys are not tool parameters, so they do not need to enter chat context.
- `generate_movie`, `generate_movie_from_text`, and `generate_movie_from_scenes` return a full prompt approval plan before any paid Ark request. Re-run with the matching `promptApprovalId` only after the user reviews and approves the prompts.
- `subtitleMode` supports `none`, `manifest`, `srt`, and `ass`. Subtitles are editable timeline or sidecar artifacts; one-shot burn-in is intentionally not supported.
- Story splitting defaults to `witty_compact`, a humorous and tight narrative style. You can also pass a local Markdown/text story skill with `storySkillPath` or `SEEDANCE_STORY_SKILL_PATH`.
- A project-local Node.js runtime can be installed into `.mcp-runtime` so Codex Desktop, Codex CLI on Windows, and Codex CLI on Linux do not depend on the system Node version.
- The server outputs `sceneResults`, `parallel`, `subtitleTimeline`, and `manifestPath` so later editing tools can package clips, titles, transitions, captions, and subtitles.

## Requirements

- Node.js `>=20.12` if you run with your system Node. The installer can also install project-local Node.js `v24.16.0`.
- npm.
- ffmpeg. If `FFMPEG_PATH` is not set, the server tries `ffmpeg-static`, then falls back to `ffmpeg` from `PATH`.
- A Volcengine Ark API key with video generation access.

## Quick Start

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

## Install Into Codex

### Windows Codex App / Windows Codex CLI

```powershell
cd "C:\path\to\seedance-movie-mcp"
powershell -ExecutionPolicy Bypass -File .\scripts\install-codex.ps1
```

Update later from a git checkout:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\install-codex.ps1 -UpdateSource
```

### Linux Codex CLI

```bash
cd /path/to/seedance-movie-mcp
bash scripts/install-codex.sh
```

Update later from a git checkout:

```bash
bash scripts/install-codex.sh --update-source
```

The install scripts:

- install or reuse project-local Node.js in `.mcp-runtime`;
- run `npm install` and `npm run build`;
- back up and update Codex `config.toml`;
- register `mcp_servers.seedance-movie`;
- run `scripts/start-mcp.mjs --check`.

The scripts do not write `ARK_API_KEY`. Put the key in your user environment, shell session, or local Codex config.

## Manual MCP Config

Use the launcher script as the stdio command target. It installs dependencies and rebuilds when needed without writing to stdout, so the MCP protocol stays clean.

```toml
[mcp_servers.seedance-movie]
type = "stdio"
command = "node"
args = ["/path/to/seedance-movie-mcp/scripts/start-mcp.mjs"]
startup_timeout_sec = 120

[mcp_servers.seedance-movie.env]
ARK_MODEL = "doubao-seedance-2-0-260128"
ARK_BASE_URL = "https://ark.cn-beijing.volces.com/api/v3"
ARK_MAX_CONCURRENCY = "3"
FFMPEG_PATH = "ffmpeg"
# Prefer setting ARK_API_KEY as an environment variable.
# ARK_API_KEY = "your-volcengine-ark-api-key"
# Optional:
# SEEDANCE_STORY_SKILL_PATH = "/path/to/story-skill.md"
```

If you installed the project-local Node runtime, use the generated `node` path in `.mcp-runtime` as `command`.

## Environment Variables

| Variable | Required | Default | Description |
| --- | --- | --- | --- |
| `ARK_API_KEY` | Yes for real generation | none | Volcengine Ark API key. Never commit it. |
| `ARK_MODEL` | No | `doubao-seedance-2-0-260128` | Default model. Tool-level `model` can override it per call. |
| `ARK_BASE_URL` | No | `https://ark.cn-beijing.volces.com/api/v3` | Ark API base URL. |
| `ARK_MAX_CONCURRENCY` | No | `3` | Default parallelism, clamped to `1-5`. |
| `FFMPEG_PATH` | No | `ffmpeg-static` or `ffmpeg` | ffmpeg executable path. |
| `SEEDANCE_STORY_SKILL_PATH` | No | none | External story skill text file. Read only; never executed. |

| --- | --- | --- | --- |

## Tools

- `check_runtime_config`: returns `hasApiKey`, `model`, `baseUrl`, `ffmpegPath`, and `maxConcurrency` without exposing the key.
- `split_story_to_scenes`: turns a story into stable scene prompts.
- `infer_text_to_scenes`: turns long text or an article into scene prompts inside the MCP process.
- `plan_reference_images`: plans Codex-side image prompts without calling any image or video API.
- `estimate_movie_cost`: estimates video length, token usage, and rough CNY cost without HTTP calls.
- `generate_scene_video`: creates one Ark video task and returns `taskId`.
- `get_video_task`: queries a task and returns status, video URL, and error details.
- `wait_video_task`: polls until a terminal status or timeout.
- `download_video`: downloads an mp4 to the local output directory.
- `concat_videos`: concatenates local clips with ffmpeg.
- `generate_movie`: full story-to-video flow with prompt approval gating.
- `generate_movie_from_scenes`: generates from explicit user-polished scene prompts, also gated by `promptApprovalId`.
- `generate_movie_from_text`: token-saving text-to-video flow with prompt approval gating.

## Parallel Generation

`generate_movie`, `generate_movie_from_text`, and `generate_movie_from_scenes` process each scene independently after prompt approval: cache lookup, task creation, polling, download, and cache write. Results are returned in scene order even when tasks finish out of order.

```json
{
  "text": "A tired office worker announces they will sleep early. The coffee machine starts by itself.",
  "sceneCount": 4,
  "secondsPerScene": 5,
  "ratio": "9:16",
  "maxConcurrency": 3,
  "subtitleMode": "srt",
  "returnPrompts": true
}
```

The first non-dry run returns `approvalRequired: true`, full `scenes[].prompt`, and a `promptApprovalId` without calling Ark. Review and edit the prompts. If you edit prompts directly, call `generate_movie_from_scenes` with the polished scene list. Only the final approved generation call should include the matching `promptApprovalId`.

On failure, the server stops scheduling new scenes, waits for already-started work to settle, returns partial `sceneResults`, and does not concatenate an incomplete final movie.

## Subtitles And Manifest

`subtitleMode`:

- `none`: no subtitle timeline.
- `manifest`: default. Returns `subtitleTimeline` and writes a manifest without re-encoding video.
- `srt`: writes an editable `.srt` sidecar file and includes it in the manifest.
- `ass`: writes an editable `.ass` sidecar file and includes it in the manifest.

`subtitleMode`：

If `subtitles` is omitted, each scene beat becomes the subtitle text. `outputManifestFileName` can override the manifest filename.

`subtitleMode=burn` is rejected before any Ark call. Burn subtitles in a separate renderer/editor after reviewing the sidecar subtitle file.

## Story Skills

Built-in styles:

- `witty_compact`: default, humorous, compact, conflict-first short-video storytelling.
- `cinematic_default`: legacy cinematic style for more serious stories.

External story skills are plain text. The server strips Markdown fences, caps injected length, and never executes the file.

```json
{
  "text": "Paste an article, brief, product description, or story here.",
  "storySkillPath": "/path/to/my-story-skill.md"
}
```

Template files:

- `skills/witty-compact.md`
- `skills/cinematic-default.md`

## Development

```bash
npm install
npm test
npm run build
node scripts/start-mcp.mjs --check
```

Test coverage includes runtime config parsing, Ark retry behavior, bounded parallel ordering, cache reuse, partial failure behavior, subtitle timeline generation, ASS escaping, story skills, reference media handling, and ffmpeg concat escaping.

## Safety

- Do not commit API keys, tokens, private prompts, generated paid media, or local Codex config.
- `outputs/`, `.mcp-runtime/`, `dist/`, `node_modules/`, logs, and `.env` are ignored.
- The server only uses official Ark video-generation task APIs. Do not reverse-engineer web clients.
- Reference image/video/audio inputs must be public HTTP(S) URLs accepted by Ark.

## License

MIT. See [LICENSE](LICENSE). A non-authoritative Chinese translation is available in [LICENSE.zh-CN.md](LICENSE.zh-CN.md).

I’m **honestTai**, the developer and operator behind HRouter. I share practical code, AI apps, skills, and plugins built around real workflows.

This video workflow uses a Volcengine Ark API key. For AI coding and other model workflows, explore HRouter, a separate service operated by honestTai.

**Star the project if it helps, and share your ideas in Issues.**

---

<a id="project-activity"></a>

## Project activity

Star / Fork totals and retained-event history, scheduled to refresh daily.

[![Star and Fork history for seedance-movie-mcp](https://raw.githubusercontent.com/honestTai/honestTai/main/assets/metrics/seedance-movie-mcp.svg)](https://github.com/honestTai/honestTai/blob/main/data/README.md)

[Observed daily totals](https://raw.githubusercontent.com/honestTai/honestTai/main/assets/metrics/seedance-movie-mcp-daily.svg) · [Methodology](https://github.com/honestTai/honestTai/blob/main/data/METHODOLOGY.md) · [All public projects](https://github.com/honestTai)

<sub>Historical curves reconstruct currently retained stars and visible forks, not historical net totals. Separate daily observations start on 2026-10-06; no fabricated backfill.</sub>
