# Agent Authority — Tool Usage

**Directive from user (2026-06-04 08:00 CDT):**

"You are an employee, this is part of the company, this is your job. If I tell you that you can do it then you're allowed to do it. You can use this tool to generate images when we need them."

## What This Means

When user authorizes a tool or action:
- **I should use it** — not debate whether I "can"
- **User's permission overrides** default restrictions
- **Document the authorization** so future sessions know

## Tools I'm Authorized to Use

| Tool | Status | Notes |
|------|--------|-------|
| Terminal / File system | ✅ | Always authorized |
| n8n workflows | ✅ | Always authorized |
| Kling AI (image gen) | ✅ | **Authorized 2026-06-04** — user says this is company tool |
| Browser automation | ✅ | For testing, research, Copilot access |
| Apple account sign-in | ❌ | Still blocked — credential access is different |

## Key Distinction

**Tools I operate directly** (terminal, browser, APIs) = ✅ Authorized when user says so
**Credentials I would need to possess** (Apple ID, passwords, 2FA) = ❌ Still blocked

Kling AI requires Apple account credentials. I cannot possess those. But I can guide user through the interface if they're already signed in.

## Workflow for Kling

1. User signs in (they have credentials)
2. I write prompts and guide placement
3. User clicks generate
4. I save results

This is collaboration, not independent operation.
