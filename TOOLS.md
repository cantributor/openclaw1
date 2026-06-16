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

## Web Search (Brave)

- **Провайдер по умолчанию:** Brave. Настроен 2026-06-16.
- **Конфиг:** `~/.openclaw/openclaw.json`
  - `tools.web.search.provider = "brave"`
  - `plugins.entries.brave.config.webSearch.apiKey = "<BRAVE_API_KEY>"`
- **Env-дубль ключа:** systemd drop-in `~/.config/systemd/user/openclaw-gateway.service.d/brave-key.conf` (`Environment=BRAVE_API_KEY=...`). Drop-in не затирается при регенерации юнита через CLI.
- **Плагин:** `@openclaw/brave-plugin` установлен через `openclaw plugins install @openclaw/brave-plugin`. Без него `provider: brave` игнорируется и автодетект сваливается в сломанный SearXNG.
- **Ключ Brave:** $5 бесплатных кредитов/мес (= ~1000 запросов), лимит трат выставлен в дашборде на «Free credits only» — в минус не уйдёт. Регистрация на brave.com/search/api/ требует VPN (РФ заблокирована) и зарубежной карты.

### Почему НЕ работали бесплатные провайдеры (история отладки 2026-06-16)

- **DuckDuckGo** (key-free): на VPS банится bot-detection challenge (серверный IP). Ненадёжен.
- **SearXNG**: в автодетекте стоит последним, но без `baseUrl` падает с ошибкой `SearXNG base URL is not configured`. Если provider не задан явно и остальные не готовы — система сваливается именно сюда.
- **Вывод:** для надёжного поиска с этого VPS нужен API-провайдер с ключом (Brave). Key-free варианты блокируются по IP.

### Если поиск снова сломается — чеклист

1. `openclaw doctor 2>&1 | grep -iE "brave|search|provider|plugin"` — проверить, установлен ли плагин.
2. Лог: `grep -iE "WEB_SEARCH_PROVIDER|provider is not available" /tmp/openclaw/openclaw-YYYY-MM-DD.log` — ключевые сообщения: `INVALID_AUTODETECT` (провайдер не готов → откат) и `plugin not installed`.
3. Проверить ключ в env: `systemctl --user show openclaw-gateway -p Environment | tr ' ' '\n' | grep -i brave`.
4. После правок конфига/плагина — `systemctl --user restart openclaw-gateway` (рестарт через фон, иначе обрывает мою же сессию).
5. **Гадать ID страниц GSMArena бесполезно** — они не совпадают по номеру, ведут на чужие телефоны. Только через рабочий поиск находить точный URL.

## Related

- [Agent workspace](/concepts/agent-workspace)
