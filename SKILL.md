---
name: fetch-asset
description: >
  WebからMP3/MP4/PNG/SVG等のメディアファイルを取得するツールセット。
  yt-dlp（動画・音声 1000+サイト）・gallery-dl（Pixiv/Twitter/X等の画像ギャラリー）・
  aria2（並列・大容量DL）・curl（直リン）・Pixabay CDN抽出スクリプトの使い分けを定義する。
  「この動画をダウンロードして」「この曲を取得して」「Pixabayの音楽が欲しい」「画像を一括DL」などで使う。
user-invocable: true
allowed-tools: Bash, Read
argument-hint: "<URL> [--audio-only]"
---

# fetch-asset

Web から MP3/MP4/PNG/SVG 等のメディアファイルを取得するツールセット。yt-dlp・gallery-dl・aria2・curl・サイト固有スクリプトの使い分けを定義する。

## サイト別ツール対応表

| カテゴリ | サイト | ツール |
|---|---|---|
| 動画・音声 | YouTube, Vimeo, SoundCloud, ニコニコ動画 等 1000+サイト | yt-dlp |
| 画像ギャラリー | Pixiv, DeviantArt, ArtStation, Flickr, Twitter/X, Instagram, Danbooru/Gelbooru, Weibo, Reddit | gallery-dl |
| Pixabay（音楽・画像）| Pixabay | scripts/extract-pixabay-cdn.js（APIキー不要） |
| Unsplash | Unsplash | Unsplash API（APIキー必要、50枚/時） |
| Freesound | Freesound | Freesound API（OAuth2必要） |
| 直リン全般 | — | curl / aria2 |

## yt-dlp（動画・音声）

### インストール

```bash
py -m pip install yt-dlp
# または
winget install -e --id yt-dlp.yt-dlp
```

ffmpeg が変換に必須（`winget install -e --id Gyan.FFmpeg`）。

### 音声のみ取得（MP3・最高品質）

```bash
yt-dlp -x --audio-format mp3 --audio-quality 0 --embed-thumbnail --embed-metadata "<URL>"
```

### 再エンコードなし（Opus/M4A のまま保存・最速）

```bash
yt-dlp -f ba -x --audio-format best "<URL>"
```

### 動画取得（最高品質 MP4 + メタデータ + 字幕）

```bash
yt-dlp --format "bv*+ba" --merge-output-format mp4 \
  --embed-thumbnail --embed-metadata \
  --write-subs --sub-lang ja \
  --output "%(title)s.%(ext)s" "<URL>"
```

### サムネイルのみ取得

```bash
yt-dlp --write-thumbnail --skip-download "<URL>"
```

## gallery-dl（画像ギャラリー）

### インストール

```bash
py -m pip install gallery-dl
# aria2 バックエンド（高速並列DL）と組み合わせる場合は aria2 も必要
winget install -e --id aria2.aria2
```

### 基本DL

```bash
gallery-dl "<URL>"
```

### 出力先・ファイル名指定

```bash
gallery-dl -D ./output --filename "{title}_{id}.{extension}" "<URL>"
```

### aria2 バックエンドで高速化

```bash
gallery-dl --external-downloader aria2c "<URL>"
```

### 認証が必要なサイト（Pixiv 等）

```bash
gallery-dl --cookies cookies.txt "<URL>"
# または
gallery-dl -u <username> -p <password> "<URL>"
```

## aria2（並列・大容量DL）

### インストール

```bash
winget install -e --id aria2.aria2
```

### 直リンを高速DL（16分割）

```bash
aria2c -x 16 -s 16 "<URL>"
```

### 複数 URL を並列DL

```bash
aria2c -j 4 -c "<URL1>" "<URL2>" "<URL3>"
```

### URL リストファイルから並列DL

```bash
aria2c -j 5 -i urls.txt
```

## Pixabay（scripts/extract-pixabay-cdn.js）

APIキー不要。headless Chrome で音楽ページをレンダリングし `cdn.pixabay.com` の MP3 直リンを抽出する。

### 使い方

```bash
node C:/@projects/eruto-skills/fetch-asset/scripts/extract-pixabay-cdn.js "<URL1>" "<URL2>"
```

JSON で結果を返す。`mp3s` 配列に直リンが入っているので aria2 や curl で DL する。

```bash
# 抽出した URL を curl でDL
node C:/@projects/eruto-skills/fetch-asset/scripts/extract-pixabay-cdn.js "<URL>" | \
  jq -r '.[].mp3s[]' | xargs -I{} curl -OL "{}"
```

依存: Google Chrome（標準パスを自動検出）、puppeteer-core（`npm install` in `scripts/`）

## curl（直リン）

Windows 11 標準搭載。シンプルな直接DLに使う。

```bash
# 単純DL
curl -OL "<URL>"

# ファイル名指定
curl -o output.mp3 "<URL>"

# レジューム
curl -C - -OL "<URL>"
```

## 失敗時の対応

| 症状 | 対処 |
|---|---|
| yt-dlp がサイトを認識しない | `yt-dlp --list-extractors` で対応サイト確認 |
| gallery-dl が非対応サイト | Pixabay/Unsplash は API スクリプトへ。他は yt-dlp を試す |
| Pixabay スクリプトが空 | プレイボタンをクリックしてから再試行。`--timeout` を延長 |
| レート制限 | aria2 の `-j` を下げる。時間を空ける |

## 依存関係

| ツール | インストール | 用途 |
|---|---|---|
| yt-dlp | `py -m pip install yt-dlp` | 動画・音声 |
| ffmpeg | `winget install Gyan.FFmpeg` | yt-dlp の変換に必須 |
| gallery-dl | `py -m pip install gallery-dl` | 画像ギャラリー |
| aria2 | `winget install aria2.aria2` | 並列DL・大容量 |
| curl | Windows 11 標準搭載 | 直リン |
| puppeteer-core | `npm install` in `scripts/` | Pixabay スクリプト |
| Google Chrome | 手動インストール | Pixabay スクリプト |
