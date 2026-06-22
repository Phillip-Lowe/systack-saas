# Disk Cleanup Session — Critical Mass Resolved

**Date:** 2026-06-12 09:42–10:14 CDT
**Status:** COMPLETE — 58GB freed, 70% capacity (was 100%)

---

## Before / After

| Metric | Before | After | Change |
|--------|--------|-------|--------|
| Used | 190GB | 132GB | **-58GB freed** |
| Free | 528MB | 58GB | **+57.5GB** |
| Capacity | 100% | **70%** | Breathing room restored |

---

## What Was Moved to External

**External drive:** `/Volumes/External/Archive-MacBook/`

| Item | Size | External Location |
|------|------|-------------------|
| Ollama models | 23GB | `AI-Models/ollama-models-backup/` |
| HuggingFace cache | 19GB | `AI-Models/huggingface-cache-backup/` |
| Organized/Other (TIFFs, Electron) | 7.4GB | `Organized-Projects/Other-backup/` |
| ElevenLabs video | ~550MB | `Organized-Projects/` |

**Note:** User confirmed "we don't use local models right now" — Ollama and HuggingFace were moved without symlinks. If local models are needed again, either copy back or create symlinks.

---

## What Was Cleaned Locally

| Item | Before | After | Savings |
|------|--------|-------|---------|
| npm cache | 4.2GB | 551MB | 3.7GB |
| uv cache (`~/.cache/uv`) | 3.7GB | 97MB | 3.6GB |
| uv share (`~/.local/share/uv`) | 778MB | 0B | 778MB |
| node cache | ~63MB | 0B | 63MB |
| Library logs | 550MB | 0B | 550MB |

---

## Remaining Large Local Items (Left Untouched — Actively Used)

- `~/Library` — 46GB (system files, Xcode, caches)
- `~/.openclaw` — 10GB (active workspaces, especially `sol` at 7.5GB)
- `~/.local` — 2.8GB (Python/pipx tools)
- `~/.vscode` — 2.4GB (editor extensions)
- `~/Pictures` — 1.6GB
- `~/ComfyUI` — 1.3GB (local image generation)
- `~/.n8n` — 1.1GB (active n8n instance)
- `~/go` — 921MB
- `~/.nvm` — 822MB
- `~/.npm` — 551MB (cleaned)
- `~/.cache` — 214MB (cleaned)

---

## Archive Structure on External

```
/Volumes/External/Archive-MacBook/
├── AI-Models/
│   ├── ollama-models-backup/          (23GB)
│   └── huggingface-cache-backup/       (19GB)
├── Organized-Projects/
│   ├── Other-backup/                   (7.4GB)
│   └── ElevenLabs_video_topaz-upscale...mp4
├── OpenClaw-Workspaces/                (empty — reserved)
├── Library-Archives/                   (empty — reserved)
└── Caches-Cleaned/                     (empty — reserved)
```

---

## Lessons

1. **External USB transfer speed:** ~14MB/s sustained. 23GB Ollama move took ~5 minutes. 7.4GB Organized/Other took ~8 minutes. Plan accordingly for future moves.

2. **macOS `mv` across volumes does copy-then-delete.** The source isn't removed until the copy completes. If interrupted, both copies may exist — verify then remove source manually.

3. **Caches add up fast:** npm (4.2GB) + uv (4.5GB combined) + node + Library logs = ~9.5GB of "invisible" space. Worth checking quarterly.

4. **AI model caches are the biggest bang for buck:** HuggingFace (19GB) + Ollama (23GB) = 42GB. More than all other cleanup combined.

---

## User Directives Captured

- **"we don't use local models right now"** → No symlinks needed for Ollama/HuggingFace. Can move back if needed.
- **"save everything everywhere and end session"** → Saved to daily memory, MEMORY.md, and wiki.
