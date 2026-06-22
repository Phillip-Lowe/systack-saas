# 2026-06-08 — Postgres Cleanup & Decision

## Time: ~09:47 CDT

## Actions Taken
1. Installed pgAdmin 4 (free browser-based Postgres tool)
2. Fixed pgAdmin connection issue — username must be lowercase `systack` not `Systack`
3. Deleted unused databases:
   - `crm` — was created for invoice parser but we use SQLite
   - `utopia_deli` — was created but never connected to n8n
4. Verified only system databases remain: postgres, template0, template1
5. Deleted TablePlus (free trial version)
6. Attempted pgAdmin Ollama AI configuration:
   - Created `~/.pgadmin/config.py` with Ollama settings
   - Created `PGADMIN-OLLAMA-SETUP.md` with manual configuration steps
   - pgAdmin requires UI-based configuration for AI/LLM features

## Key Decision
**Postgres is now our primary database.** Going forward, all new data will go into Postgres instead of SQLite.

## Credential Registry Created
File: `credentials/SYSTACK-CREDENTIALS-REGISTRY.md`
- Complete registry of all Systack credentials
- Includes retrieval instructions from keychain and files
- Last verified 2026-06-08

## Files
- `credentials/SYSTACK-CREDENTIALS-REGISTRY.md`
- `PGADMIN-OLLAMA-SETUP.md`
- `~/.pgadmin/config.py`

## pgAdmin + Ollama Setup Status
- pgAdmin 4 v9.15 has built-in AI/LLM features (AI Reports)
- Supports Ollama as a local provider (free, no API key)
- User config override created at `~/.pgadmin/config.py`
- Manual UI configuration needed in pgAdmin Preferences → AI
- See `PGADMIN-OLLAMA-SETUP.md` for step-by-step instructions

## Status
Ready for Postgres-first development. pgAdmin Ollama integration configured but requires manual UI setup to activate.
