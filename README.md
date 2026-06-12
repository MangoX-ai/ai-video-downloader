# douyin-crawl-skills

Claude Code skills để crawl, tải và lồng tiếng Việt cho video Douyin — chạy hoàn toàn tự động, không hỏi lại user.

## Skills có sẵn

| Skill | Mô tả |
|-------|-------|
| `/douyin-crawler <url>` | Crawl danh sách video từ profile / feed page Douyin |
| `/douyin-generator <folder>` | Dịch transcript + TTS + ghép video → `output_vi.mp4` |

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
douyin/.venv/bin/python3 -m douyin jingxuan --url "<URL>" --max 10 --no-headless
```

Output: JSON list các video với `aweme_id`, `detail_url`, `author`, `title`.

---

## /douyin-generator

Pipeline đầy đủ: `index.mp4` → `transcript.json` → `transcript-vi.json` → `dub_vi.mp3` → `output_vi.mp4`.

```bash
/douyin-generator downloads/jingxuan/<video-folder>

# Chỉ định giọng đọc
/douyin-generator downloads/jingxuan/<video-folder> --voice sg_female_thaotrinh_full_44k-phg.mp3

# Dùng provider khác
/douyin-generator downloads/jingxuan/<video-folder> --provider vbee
```

AI tự chọn voice từ `voice/<provider>.csv` dựa trên nội dung video — không hỏi user.

---

## Cấu trúc downloads

```
downloads/<author>/<video-title>__<id>/
  ├── index.mp4           ← video gốc
  ├── metadata.json
  ├── transcript.json     ← transcript tiếng Trung (Whisper)
  ├── transcript-vi.json  ← bản dịch tiếng Việt
  ├── dub_vi.mp3          ← audio TTS
  └── output_vi.mp4       ← video hoàn chỉnh
```

---

## Môi trường

- Python venv: `douyin/.venv/bin/python3`
- OmniVoice TTS server: `http://192.168.1.61:8002`
- Providers TTS hỗ trợ: OmniVoice (mặc định), Vbee, ElevenLabs, OpenAI

## Cài đặt

```bash
cd douyin
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/playwright install chromium
```

## Lưu ý

- Chỉ dùng cho mục đích cá nhân / nghiên cứu
- Tuân thủ ToS của Douyin
- Nếu crawl ra 0 video, thêm `--no-headless` để browser hiện lên — có thể cần login hoặc vượt captcha
