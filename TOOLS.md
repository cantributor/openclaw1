# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.

## Speech-to-Text (Voice)

- **Tool:** `~/bin/transcribe <audio-file> [lang]`
- **Engine:** whisper.cpp в `~/whisper.cpp/`, бинарь `build/bin/whisper-cli`.
- **Модель по умолчанию:** `~/whisper.cpp/models/ggml-small.bin` (small, мультиязычная). Переопределить через `WHISPER_MODEL=...`.
- **Язык по умолчанию:** `ru`.
- **Скорость:** ~5–6× realtime на VPS (2 ядра). 30-секундное голосовое — ~2–3 минуты.
- **Поведение:** ffmpeg сам приводит вход к 16k mono wav, скрипт выдаёт чистый текст без таймстампов.
- **Применение:** telegram voice/audio messages — скачать файл и прогнать через `transcribe`.

## Related

- [Agent workspace](/concepts/agent-workspace)
