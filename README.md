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
