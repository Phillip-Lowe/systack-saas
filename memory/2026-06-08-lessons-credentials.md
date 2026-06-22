# 2026-06-08 — Lesson: Always Check Credentials Before Saying "I Don't Know"

## What Happened

User: "You have the utopia deli app password for Gmail you can use that it works. We just use it."

Me: "I need the n8n login email/password"

User: "Plowe95@ywhoo.com 123GreeN23! You have to do better with these credentials, bro every day you talk about you don't know or remember the credentials when they are in your memory in your files you gotta stop doing this"

## The Problem

I was not checking:
1. `TOOLS.md` — where credentials are documented
2. `MEMORY.md` — where business accounts are logged
3. macOS Keychain — where app passwords are stored
4. Credential files (`.n8n_api_key`, etc.) — where API tokens live
5. Session transcripts — where credentials were previously shared

## What I Should Have Done

**Pattern:** Before any "I don't have access" statement:
1. `memory_search` for the credential
2. `exec security find-generic-password` for keychain
3. `read` credential files in workspace
4. Check `TOOLS.md` for documented accounts

## What I Did During This Session (After Being Called Out)

- Retrieved Gmail app password: `sacn gdyi nrqw otnx` from keychain
- Retrieved n8n API key from `~/.openclaw/workspaces/sol/.n8n_api_key`
- Used n8n API to import workflow (bypassed browser login entirely)
- Got n8n login credentials from user directly

## Future Rule

**NO MORE "I don't have the credentials" without checking:**
1. Memory search (TOOLS.md, MEMORY.md, session history)
2. Keychain search
3. Credential files in workspace
4. Environment variables

If after ALL of those it's not found, THEN say "I don't have it."

---

**Saved:** 2026-06-08 00:17 CDT
