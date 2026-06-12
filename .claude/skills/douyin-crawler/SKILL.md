---
name: douyin-crawler
description: >
  Crawl một trang Douyin bất kỳ (jingxuan, user profile, hoặc URL detail có modal_id)
  và trả về JSON list các video với aweme_id, detail_url, author, title.
  Dùng skill này khi người dùng cung cấp link Douyin và muốn lấy danh sách video,
  lấy link clip, hoặc muốn biết có bao nhiêu video trên một trang/profile.
  Cũng dùng khi người dùng paste link https://www.douyin.com/... và muốn crawl,
  extract, hoặc liệt kê video — ngay cả khi họ không dùng từ "crawl".
metadata:
  version: 1.1.0
  author: MangoAds Co., Ltd.
  copyright: Copyright (c) 2024-2026 MangoAds Co., Ltd. All rights reserved.
---

# Douyin Crawler Skill

Crawl trang Douyin → **tải luôn** (không hỏi lại người dùng).

## Mặc định

- **Số video mặc định: 10** — tải luôn 10 video đầu tiên nếu user không chỉ định số lượng.
- **Không dùng `--dry-run`** — tải thực sự, không list rồi hỏi.
- Nếu user chỉ định số lượng (ví dụ "tải 30 video") thì dùng `--max <số đó>`.

## Cấu trúc project

```
douyin-downloader/
├── .claude/skills/douyin-crawler/SKILL.md
├── douyin/
│   ├── .venv/           ← virtualenv
│   ├── __init__.py
│   ├── __main__.py      ← entry point
│   ├── cli.py           ← CLI commands
│   ├── crawler.py       ← Playwright crawler
│   ├── downloader.py    ← async HTTP downloader
│   ├── store.py         ← index.csv read/write
│   ├── transcribe.py    ← faster-whisper
│   ├── pyproject.toml
│   └── requirements.txt
├── downloads/
└── index.csv
```

## Cách chạy

```bash
douyin/.venv/bin/python3 -m douyin <command> [options]
```

### 1. Feed page (jingxuan, channel, category)

Mặc định 10 video, tải luôn:

```bash
douyin/.venv/bin/python3 -m douyin jingxuan --url "<URL>" --max 10 --no-headless
```

Nếu user chỉ định số lượng (ví dụ 30):

```bash
douyin/.venv/bin/python3 -m douyin jingxuan --url "<URL>" --max 30 --no-headless
```

### 2. User profile

```bash
douyin/.venv/bin/python3 -m douyin user "<URL>" --max 10
```

### 3. Video detail / link trực tiếp

```bash
douyin/.venv/bin/python3 -m douyin dl "<URL>"
```

## Output format

Sau khi lấy được danh sách, trình bày cho người dùng dưới dạng JSON:

```json
[
  {
    "aweme_id": "7548734306391215395",
    "detail_url": "https://www.douyin.com/video/7548734306391215395",
    "author": "秋芝2046",
    "title": "教你10分钟打造专属AI客服..."
  }
]
```

`detail_url` luôn có dạng `https://www.douyin.com/video/{aweme_id}`.

## Lưu ý

- Nếu output là `0 video`, thêm `--no-headless` để browser hiện ra — có thể cần login hoặc vượt captcha.
- Feed page Douyin thường giới hạn ~50 video/trang dù đặt `--max` cao hơn.
- `index.csv` ở root track trạng thái (pending/success/failed) và caption whisper của từng video.
