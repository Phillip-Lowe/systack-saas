# Validation Environment Policy (V.E.P.)

**Version:** 1.0
**Created:** June 8, 2026
**Owner:** VALI (enforcement)
**Status:** ACTIVE

---

## CORE RULE

**NEVER modify production. NEVER deploy untested.**

Every agent must:
1. Build and test in sandbox/isolated environment FIRST
2. Document the outcome (success, failure, steps)
3. Only then consider production deployment
4. Production changes require explicit user approval (per AGENTS.md Rule 3A)

---

## ENVIRONMENT DEFINITIONS

| Environment | Purpose | Data | Risk Level |
|-------------|---------|------|------------|
| **Local/Sandbox** | Development, experimentation, testing | Fake/test data only | Low |
| **Staging** | Pre-production validation, integration testing | Anonymized snapshot | Medium |
| **Production** | Live customer-facing systems | Real customer data | High — REQUIRES APPROVAL |

**Current Staging Gap:** No formal staging environment exists. Local = sandbox.

---

## SANDBOX REQUIREMENTS BY SYSTEM

### n8n Workflows
| Component | Sandbox Setup | How to Test |
|-----------|--------------|-------------|
| Webhook triggers | Use test webhook URLs (`https://webhook.site/...`) | Post test payloads, verify response |
| Square API | Square Sandbox environment (separate from prod) | Create test payment links, verify no real charges |
| Google Sheets | Create `TEST-` prefixed sheet | Write/read test rows, delete after |
| Email nodes | Use `https://webhook.site/` or Mailtrap | Verify email payload without sending |
| Code nodes | Test in isolated n8n workflow first | Validate output with test data |

**Square Sandbox:** https://developer.squareup.com/reference/square/sandbox

### Website/HTML
| Component | Sandbox Setup | How to Test |
|-----------|--------------|-------------|
| Frontend forms | `file://` or `localhost` | Fill with fake data, verify console |
| Webhook POST | Route to `https://webhook.site/` or local echo server | Verify payload structure |
| CSS/JS changes | Browser dev tools or local server | Visual check, responsive test |

### Databases
| Component | Sandbox Setup | How to Test |
|-----------|--------------|-------------|
| SQLite | Copy production DB, rename to `test-` | Run queries, verify schema |
| Schema changes | Apply to test copy first | Test reads/writes, rollback if needed |

### Skills/Scripts
| Component | Sandbox Setup | How to Test |
|-----------|--------------|-------------|
| Python scripts | `if __name__ == "__main__":` with mock inputs | Run locally, verify output |
| SKILL.md | Test in isolated session first | Verify tool calls, error handling |

---

## VALIDATION CHECKLIST (MANDATORY)

Before ANY production change, agent must complete:

```
[ ] Tested in sandbox/local environment
[ ] Used fake/test data (never production data in sandbox)
[ ] Documented outcome in memory/learning/YYYY-MM-DD-<role>.md
[ ] Included: what was tested, steps taken, result, any errors
[ ] If failed: documented why, what was tried, next attempt
[ ] If passed: documented exact steps for reproduction
[ ] Requested user approval for production deployment (if applicable)
```

**NO CHECKLIST = NO PRODUCTION DEPLOYMENT**

---

## DOCUMENTATION REQUIREMENTS

Every test must produce:

```markdown
## Test: <Topic>
**Date:** YYYY-MM-DD
**Agent:** <ROLE>
**Environment:** Local/Sandbox/Staging
**Objective:** <What was being tested>

### Steps Taken
1. <Step 1>
2. <Step 2>

### Results
- <What happened>
- <Output/screenshot/link>

### Errors (if any)
- <Error message>
- <Root cause (if known)>

### Next Steps
- <What to try next>
- <Or: Ready for production approval>
```

---

## WHAT REQUIRES SANDBOX TESTING

| Change Type | Sandbox Required? | Approval Required? |
|-------------|-------------------|-------------------|
| New n8n workflow | ✅ Yes | ✅ Yes (before activation) |
| Edit existing n8n workflow | ✅ Yes | ✅ Yes |
| Database schema change | ✅ Yes | ✅ Yes |
| Website form/JS change | ✅ Yes (local test) | ⚠️ Optional for minor fixes |
| CSS/style changes | ⚠️ Optional (visual check) | ❌ No |
| Copy/content updates | ⚠️ Optional | ❌ No |
| New skill creation | ✅ Yes (isolated test) | ✅ Yes |
| Cron job modification | ✅ Yes | ✅ Yes |
| Config changes (.env, etc.) | ✅ Yes | ✅ Yes (AGENTS.md Rule 3A) |

---

## SANDBOX TOOLS AVAILABLE

| Tool | Use For |
|------|---------|
| `https://webhook.site/` | Test webhook payloads |
| `https://httpbin.org/` | Test HTTP requests/responses |
| Square Sandbox API | Test payments without real money |
| Local SQLite copy | Test database operations |
| `file://` / `python -m http.server` | Test HTML/JS locally |
| Isolated OpenClaw sessions | Test skills/scripts |

---

## CONSEQUENCES

- **First violation:** Warning + mandatory documentation
- **Repeated violation:** Agent loses autonomous deployment privileges
- **Production incident:** Full audit + AGENTS.md update + user notification

---

*Next review: After first week of testing (June 15)*
*Enforced by: VALI*
*Approved by: Green (June 8, 2026)*
