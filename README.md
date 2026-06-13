# Video Downloader & Voice Over

Bộ Claude Code skills giúp bạn **tải video nước ngoài về máy và lồng tiếng Việt (Hoặc ngôn ngữ của bạn mong muốn)** để xem offline — không cần hiểu tiếng nước đó.

Hỗ trợ hầu hết các nền tảng phổ biến: YouTube, TikTok, Douyin, Instagram, Facebook, Bilibili, Twitter/X, Vimeo và 1000+ trang khác.

---

## Cách dùng cơ bản

**Bước 1 — Tải video về:**

```
/video-download https://www.youtube.com/watch?v=...
```

**Bước 2 — Lồng tiếng Việt:**

```
/voice-over downloads/youtube/<video-folder>
```

Xong. Tool tự động transcript → dịch → TTS → ghép video, không hỏi lại.

---

## Skills

| Skill | Mô tả |
|-------|-------|
| `/video-download <url>` | Tải video từ YouTube, TikTok, Instagram, Facebook, Bilibili, Douyin, v.v. |
| `/voice-over <folder>` | Tự động dịch + lồng tiếng Việt → xuất `output_vi.mp4` |
| `/douyin-crawler <url>` | Tải hàng loạt video từ profile hoặc feed page Douyin |

---

## /video-download

Tải video chất lượng cao bằng **yt-dlp**, lưu về máy theo cấu trúc thư mục gọn gàng.

```bash
# Một video
/video-download https://www.youtube.com/watch?v=...

# Nhiều link cùng lúc
/video-download https://... https://...

# Tải cả playlist
/video-download https://www.youtube.com/playlist?list=... tải cả playlist
```

Output lưu tại `downloads/<platform>/<title>__<id>/index.mp4` — dùng được luôn với `/voice-over`.

---

## /voice-over

Tự động hoá toàn bộ pipeline từ video gốc đến video tiếng Việt:

```
index.mp4 → transcript → dịch Việt → TTS → ghép audio → output_vi.mp4
```

```bash
/voice-over downloads/youtube/<video-folder>

# Chỉ định giọng đọc
/voice-over downloads/youtube/<video-folder> --voice sg_female_thaotrinh_full_44k-phg.mp3

# Chỉ định provider TTS
/voice-over downloads/youtube/<video-folder> --provider vbee
```

Tool tự đọc `.env` để phát hiện provider đang có, tự chọn giọng phù hợp với nội dung — không hỏi user.

### Providers TTS hỗ trợ

| Key trong `.env` | Provider |
|---|---|
| `VBEE_TOKEN` + `VBEE_APP_ID` | Vbee |
| `ELEVENLABS_API_KEY` | ElevenLabs |
| `OPENAI_API_KEY` | OpenAI |
| (không có key) | OmniVoice (server LAN) |

---

## /douyin-crawler

Tải hàng loạt video từ Douyin (feed page, profile, hoặc link detail).

```bash
# Feed page Tinh Tuyển
/douyin-crawler https://www.douyin.com/jingxuan

# Profile người dùng
/douyin-crawler https://www.douyin.com/user/MS4wLjABAAAA...

# Chỉ định số lượng
/douyin-crawler https://www.douyin.com/jingxuan --max 30
```

> Nếu tải được 0 video, thêm `--no-headless` để browser hiện lên — có thể cần đăng nhập hoặc vượt captcha.

---

## Cấu trúc thư mục sau khi tải

```
downloads/<platform>/<video-title>__<id>/
  ├── index.mp4           ← video gốc
  ├── index.info.json     ← metadata
  ├── transcript.json     ← transcript gốc (Whisper)
  ├── transcript-vi.json  ← bản dịch tiếng Việt
  ├── subtitle_vi.srt     ← subtitle tiếng Việt
  ├── dub_vi.mp3          ← audio TTS
  └── output_vi.mp4       ← video hoàn chỉnh
```

---

## Cài đặt

```bash
# Dependencies hệ thống
brew install ffmpeg yt-dlp

# Python venv
cd backend
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/playwright install chromium
```

Tạo file `.env` ở project root và điền key TTS provider bạn muốn dùng (bỏ qua nếu dùng OmniVoice server LAN):

```env
VBEE_TOKEN=...
VBEE_APP_ID=...
# hoặc
ELEVENLABS_API_KEY=...
# hoặc
OPENAI_API_KEY=...
```

---

## Miễn trừ trách nhiệm

Tool này được xây dựng **chỉ cho mục đích cá nhân** — giúp người dùng tải và hiểu nội dung video nước ngoài để xem offline.

Người dùng chịu trách nhiệm đảm bảo việc sử dụng tool tuân thủ điều khoản dịch vụ của nền tảng tương ứng và pháp luật hiện hành. Tác giả không chịu trách nhiệm với bất kỳ hành vi sử dụng nào ngoài mục đích trên.

---

## License

[MIT](LICENSE)
