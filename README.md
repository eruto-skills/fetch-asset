# fetch-asset

> Claude Code skill — Download media files from the web

Web から MP3/MP4/PNG/SVG 等のメディアファイルを取得するツールセット。yt-dlp・gallery-dl・aria2・curl・サイト固有スクリプトの使い分けを定義する。

## 対応ツール

| 用途 | ツール |
| - | - |
| 動画・音声（YouTube 等 1000+サイト） | yt-dlp |
| 画像ギャラリー（Pixiv, Twitter/X 等） | gallery-dl |
| Pixabay 音楽・画像（APIキー不要） | scripts/extract-pixabay-cdn.js |
| 並列・大容量ダウンロード | aria2 |
| 直リン全般 | curl |

## Installation

```
/plugin install fetch-asset@eruto-skills
```

## Dependencies

- yt-dlp + ffmpeg
- gallery-dl
- aria2
- puppeteer-core（Pixabay スクリプト用）
- Google Chrome

## License

MIT

## Codex / Claude Code installation

This package supports both Codex and Claude Code. The plugin entry point is
`skills/fetch-asset/SKILL.md`; the root `SKILL.md` remains the standalone source.

For Codex, add the public `eruto-skills` marketplace in the plugin UI using
`https://github.com/eruto-skills/marketplace`, then install `fetch-asset`.
To install as a standalone user skill instead:

```bash
mkdir -p ~/.agents/skills
git clone https://github.com/eruto-skills/fetch-asset.git ~/.agents/skills/fetch-asset
```

On Windows PowerShell:

```powershell
New-Item -ItemType Directory -Force "$env:USERPROFILE/.agents/skills" | Out-Null
git clone https://github.com/eruto-skills/fetch-asset.git "$env:USERPROFILE/.agents/skills/fetch-asset"
```

In Codex, select the installed skill by name or invoke `$fetch-asset` with a task.
In Claude Code:

```text
/plugin marketplace add eruto-skills/marketplace
/plugin install fetch-asset@eruto-skills
```

The instructions use the tools available in the current host. Scripts are resolved
from the actual skill directory, rather than a fixed author path. Additional browser,
Python, or format-specific dependencies are described in `SKILL.md` and the references;
installing the plugin alone does not install those external programs.

## Maintaining the plugin package

Edit the root `SKILL.md` and its supporting resources, then run:

```bash
node scripts/package-plugin.mjs
node scripts/package-plugin.mjs --check
```

Commit the generated `skills/` files with the source changes. CI checks that both
layouts match, including the Claude manifest. Do not edit generated files directly.
