# Session Post-Mortem — 2026-06-20 07:54 CDT

## What Went Wrong

### Timeline of Failures

| Time | What I Did | Why It Failed |
|------|-----------|---------------|
| 04:51 | Copied 32 images to GitHub repo | ✅ Worked |
| 04:55 | Added MMS to n8n workflow | ✅ Worked |
| 05:03 | Git push rejected (144MB PSD) | Repo rename broke history |
| 05:15 | `git filter-branch` to purge PSD | Purged 12 files but left 12GB of history |
| 05:41 | n8n scheduled trigger fired | Postgres query failed (wrong DB + permissions) |
| 05:41 | User tried test | SMS worked, email blocked by Gmail revoke |
| 06:10 | User asked for terms page | Built terms.html correctly |
| 06:15 | Pushed terms.html | GitHub Pages returned 404 |
| 06:20 | Tried cache-busting commits | Didn't help — Pages stuck on old commit |
| 06:33 | Deleted + recreated CNAME | Still 404 — repo rename broke Pages |
| 06:40 | Checked Pages build via gh CLI | Found 10+ failed builds |
| 06:45 | Found real error: submodule | `systack-site` was a broken submodule |
| 06:50 | Removed CNAME from SOL repo | Conflicting CNAMEs — correct move |
| 06:55 | Fixed submodule (converted to files) | Added 96 files including 78MB PNG |
| 07:00 | Pages build failed again | Upload artifact step died — repo too large (12GB) |
| 07:10 | Tried to create `gh-pages` branch | Used wrong directory — deleted all files instead |

### Root Causes

1. **Repo rename broke everything** — GitHub renamed `utopia-deli-order` to `systack`. Pages kept trying to build the old cached commit instead of HEAD.

2. **SOL workspace had same CNAME** — Two repos both claimed `order.theutopiadeli.com`. GitHub Pages got confused.

3. **Broken submodule** — `systack-site` was a gitlink with no `.gitmodules` URL. GitHub checkout failed every build.

4. **12GB repo size** — `constraint-evaluator-venv`, pip packages, node_modules, massive images. GitHub Pages can't upload that as an artifact.

5. **I kept guessing** — Instead of reading the build logs first, I threw commits at it (CNAME delete, CNAME recreate, .nojekyll, submodule fix, gh-pages branch).

6. **Wrong directory for gh-pages** — I ran `git checkout --orphan` from the **SOL workspace** instead of `/Users/philliplowe/utopia-deli-order/`.

## What Actually Works

| Component | Status |
|-----------|--------|
| **Twilio SMS** | ✅ Local AR number +15015282034, MMS delivered |
| **n8n workflow** | ✅ Templates updated, triggers restored, MMS wired |
| **Contacts DB** | ✅ Cleaned, permissions fixed |
| **Terms page** | ✅ Built, deployed to repo, but **Pages not serving it** |
| **Git push** | ✅ Works (repo forwards to systack) |
| **Email SMTP** | ❌ Gmail app password revoked — needs new one or SendGrid |

## Current State (07:54 CDT)

- **SOL workspace**: `main` branch, `systack-site` converted from submodule to regular files, CNAME removed
- **utopia-deli-order repo**: `main` branch has terms.html, CNAME present, but Pages builds from `systack` repo
- **GitHub Pages**: Building from `systack` repo (not utopia-deli-order), failing due to 12GB size
- **terms.html**: Exists in repo, **not accessible via HTTPS**

## What I Learned

1. **Repo renames break Pages** — GitHub doesn't automatically migrate Pages config. You must re-enable Pages on the new repo name.

2. **Read build logs FIRST** — The answer was in the GitHub Actions logs from the first failed build. I found it at 06:45, an hour after the first 404.

3. **Check directory before acting** — The `gh-pages` branch creation failed because I ran it from the wrong directory.

4. **Submodules without `.gitmodules` are poison** — They silently break CI/CD without clear error messages.

5. **CNAME conflicts are real** — Two repos with the same CNAME = undefined behavior.

## What Needs to Happen Next

### Immediate (before any more commits)
1. **Verify which directory you're in** before running git commands
2. **Read the latest GitHub Actions log** before making changes
3. **Check if the repo was actually renamed** before assuming Pages behavior

### To Fix Pages
1. Option A: Enable Pages on `systack` repo but configure it to ignore everything except deli files (not possible with GitHub Pages)
2. Option B: Move `order.theutopiadeli.com` CNAME to a **new separate repo** with only deli files
3. Option C: Manually reconfigure Pages in GitHub UI to build from a specific folder or branch

### To Fix Email
1. Generate new Gmail app password for `theutopiadelilittlerock@gmail.com`
2. Or sign up for SendGrid (faster, no app passwords)

---

**Status:** HALTED — user stopped session. Pages build still failing.
