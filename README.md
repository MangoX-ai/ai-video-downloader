# douyin-crawl-skills

Claude Code skills để crawl, tải và lồng tiếng Việt cho video — chạy hoàn toàn tự động, không hỏi lại user.

## Skills có sẵn

| Skill | Mô tả |
|-------|-------|
| `/douyin-crawler <url>` | Crawl danh sách video từ profile / feed page Douyin và tải luôn |
| `/video-download <url>` | Tải video từ YouTube, TikTok, Instagram, Facebook, Bilibili, v.v. bằng yt-dlp |
| `/voice-over <folder>` | Dịch transcript + TTS + ghép video → `output_vi.mp4` |

---

## /douyin-crawler

Crawl trang Douyin → tải luôn video (mặc định 10 video).

```bash
# Feed page (Tinh Tuyển / jingxuan)
/douyin-crawler https://www.douyin.com/jingxuan

# Profile người dùng
/douyin-crawler https://www.douyin.com/user/MS4wLjABAAAA...

# Tải 30 video
/douyin-crawler https://www.douyin.com/jingxuan --max 30
```

Dưới hood, skill chạy:

```bash
backend/.venv/bin/python3 -m backend jingxuan --url "<URL>" --max 10 --no-headless
```

Output: JSON list các video với `aweme_id`, `detail_url`, `author`, `title`.

> Nếu crawl ra 0 video, thêm `--no-headless` để browser hiện lên — có thể cần login hoặc vượt captcha.

---

## /video-download

Tải video từ bất kỳ link nào bằng **yt-dlp** (YouTube, TikTok, Facebook, Instagram, Twitter/X, Bilibili, Vimeo, 1000+ trang khác).

```bash
# Tải một video
/video-download https://www.youtube.com/watch?v=...

# Nhiều link
/video-download https://... https://...

# Tải cả playlist
/video-download https://www.youtube.com/playlist?list=... tải cả playlist
```

Output lưu tại `downloads/<extractor>/<title>__<id>/index.mp4`, cùng cấu trúc với Douyin — dùng được luôn với `/voice-over`.

> Với link `douyin.com` hãy dùng `/douyin-crawler` thay vì skill này.

---

## /voice-over

Pipeline đầy đủ: `index.mp4` → `transcript.json` → `transcript-vi.json` → `subtitle_vi.srt` → `dub_vi.mp3` → `output_vi.mp4`.

```bash
/voice-over downloads/jingxuan/<video-folder>

# Chỉ định giọng đọc
/voice-over downloads/jingxuan/<video-folder> --voice sg_female_thaotrinh_full_44k-phg.mp3

# Dùng provider khác
/voice-over downloads/jingxuan/<video-folder> --provider vbee
```

AI tự chọn provider từ các key có trong `.env`, tự chọn voice từ `voice/<provider>.csv` dựa trên nội dung video — không hỏi user.

### Ưu tiên provider

| Key trong `.env` | Provider |
|---|---|
| `VBEE_TOKEN` + `VBEE_APP_ID` | Vbee |
| `ELEVENLABS_API_KEY` | ElevenLabs |
| `OPENAI_API_KEY` | OpenAI |
| (không có key) | OmniVoice (server LAN) |

---

## Cấu trúc downloads

```
downloads/<author>/<video-title>__<id>/
  ├── index.mp4           ← video gốc
  ├── metadata.json       ← metadata (hoặc index.info.json với yt-dlp)
  ├── transcript.json     ← transcript tiếng Trung (Whisper)
  ├── transcript-vi.json  ← bản dịch tiếng Việt
  ├── subtitle_vi.srt     ← subtitle tiếng Việt
  ├── dub_vi.mp3          ← audio TTS
  └── output_vi.mp4       ← video hoàn chỉnh
```

---

## Môi trường

- Python venv: `backend/.venv/bin/python3`
- OmniVoice TTS server: `http://192.168.1.61:8002` (server LAN, không cần key)
- Providers TTS hỗ trợ: OmniVoice · Vbee · ElevenLabs · OpenAI

## Cài đặt

```bash
cd backend
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/playwright install chromium
```

Cần `ffmpeg` và `yt-dlp` để dùng `/video-download` và ghép video:

```bash
brew install ffmpeg yt-dlp
```

---

## License

[MIT](LICENSE)
