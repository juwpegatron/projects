# HANDOFF — Orbis Work (recovered from the stuck "Orbis Loker" session)

> **Why this file exists.** The OpenCode session titled **"Orbis Loker"**
> (`ses_e9eabb1f1ffeXWdAZtI71fMg7r`) grew too large to auto-compact
> ("Session too large to compact — context exceeds model limit even after
> stripping media"). Its history was exported locally and compressed into this
> handoff so a **new session can continue the work with the memory intact**.
> Recovered on 2026-10-22.

---

## 0. Provenance / where the memory came from
- Recovery folder: `C:\Users\juwen_lie\Documents\Default Project\orbis-loker-recovery\`
  - `HANDOFF.md` (this file) — the distilled memory
  - `dialogue.md` — full user/assistant text from the old session (~100 KB)
  - `transcript.md` — same + tool calls/outputs (compressed, ~271 KB)
  - `files-touched.txt` — every file path the old session wrote to

---

## 1. The project
- **Path:** `C:\Users\juwen_lie\Documents\Default Project`
- **Git repo**, branch `main`, remote `https://github.com/juwpegatron/projects.git` (**public**)
- **Monorepo** of zero-dependency apps: `apps/orbis`, `apps/peers`, `apps/house-of-articles`
  - `apps/loker` and `apps/orbis-work` were **merged into Orbis and deleted** (commits `ee6f945`).
- `git` is installed but **not on PATH**: `C:\Users\juwen_lie\AppData\Local\Programs\Git\cmd\git.exe`

## 2. What the (old) session accomplished
Started as a strategic question about cloning **BOSS Zhipin** (Chinese chat-first job app) for
Indonesia → became a build:
1. Built **Loker** — a chat-first job platform for Indonesia (4th app), with a **"Verified
   Employer" anti-scam wedge**, AI match %, bilingual EN⇄ID, login/signup, offline single-file HTML
   + stdlib Python backend.
2. Mirrored **zhipin.com**'s core loop (dense job cards, power filters, company pages, chat
   tabs/templates/action bar, dual-mode seeker⇄recruiter, virtual résumé, mass-apply, VIP).
3. Pushed to GitHub (`juwpegatron/projects`) + GitHub Pages; added Obsidian notes.
4. Merged **Orbis + Loker** → `apps/orbis-work`; then **integrated it back INTO `apps/orbis`**
   (deleting `loker` + `orbis-work`).
5. Final "ideal" step: **restored Orbis's original richer app and ported the marketplace into its
   built-in `work` view** (commit `846d564`), keeping Orbis's indigo/teal look.

### Current product state — **Orbis Work inside `apps/orbis`**
- `apps/orbis/web/app.html` (desktop, ~148 KB) + `apps/orbis/web/app-mobile.html` (~161 KB)
- `apps/orbis/server/server.py` (stdlib, **port 8765**, 27-key state) — **Orbis is server-first
  (uses `fetch`)**; the `work` collection seed = roles(6)/employers(4)/candidates(5)/chats/saved.
- Orbis's `work` view now holds: **filters** (city / type / category pills), **open roles**
  (6 dense cards: salary, tags, VERIFIED badge, match %, **Apply**), **Candidates** (recruiter, 5),
  plus Orbis's original "How it works" + "Fairness rules" cards.
- **The bridge (why it's a real integration):**
  1. **Aura → résumé** (Orbis profile is the candidate's résumé)
  2. **verificationLevel → trust** (verified badges)
  3. **Apply → Commitment + a Today follow-up + an audit entry** (consent)
  4. **Fairness:** match score is **skills-only** (never personality/Aura)
- Verified: `app.html` + `app-mobile.html` → 6 role cards, 5 candidates, Apply→(commitment/Today/audit = 6 each), **0 console errors**; CI **5/5** green.

## 3. Git state (important)
| Commit | What | Status |
|---|---|---|
| `846d564` | orbis: port Orbis Work marketplace into Orbis's original app (keep its look) | ✅ pushed, **CI + Pages green** — **last known good** |
| `ee6f945` | integrate Orbis Work into Orbis; remove loker + orbis-work | ✅ |
| `238f447` | orbis-work: merge Orbis + Loker into one app | ✅ |

**UNCOMMITTED / UNFINISHED (must resolve):** `apps/orbis/web/app.html` has **dangling partial
edits** from the interrupted last task:
- `onclick="massApplyWork()"` — **`massApplyWork()` is NOT defined** (clicking it errors)
- Empty cards with **no renderers**: `#workCompaniesList` ("Companies"), `#workChatList` +
  `#workChatThread` ("Messages")
- `app-mobile.html` is **untouched** (still at committed state) → desktop/mobile mismatch.
- Also unrelated dirty files: `agents-dashboard/*`, `agents-lab/*` (**do not commit these**).

## 4. ⏳ PENDING TASK (exactly where it stopped)
The user said **"yes add them"** — i.e. add to Orbis's `work` view (BOTH `app.html` and
`app-mobile.html`), wired to Orbis's engine (`navigateTo`/`renderWork`, `state.work`,
`state.commitments`, `state.todayItems`, `state.auditLog`, `persist`):
1. **Company pages** — company profile (size, funding stage, rating + reviews, open roles).
2. **Employer chat** — chat filter tabs (All/Unread/New/Interview), **prewritten message
   templates**, action bar (Call / Share contact / Send résumé / Not interested).
3. **Mass-apply**.
Then: clean up the dangling partial edits, run CI, verify in a real browser, and (optionally)
push to GitHub + update Obsidian.

## 5. Repo contract (MUST follow)
- Layout: `apps/<name>/{web,server,tests,docs}` + `README.md`.
- Backend: **Python 3 stdlib only** (`http.server`, `sqlite3`, `json`) — no `pip install`, no build step.
- Tests: hermetic, auto-discovered by `scripts/run_all_tests.py` (exit 0/1).
- CI: `scripts/build_site.py` → `scripts/check_site.py` → `scripts/run_all_tests.py`; Pages deploy.
- Ports: **orbis 8765**, peers 8788, house-of-articles 8789.
- Pages gotchas checked by `check_site.py`: **absolute paths** and **filename case**.
- ⚠️ **Orbis is server-first (`fetch`)**, unlike peers/house-of-articles which are offline-first
  (no `fetch`). Match the file you are editing.

## 6. Hard-won cautions (from bugs hit last time)
- **Verify subagent file claims against the filesystem** (repo policy) — subagents once reported
  success while shipping a 14 KB stub, a shrunk file, and lost features.
- **Preserve UTF-8** — a subagent once corrupted the encoding → mojibake (`â€`, `ðŸ`).
- **Never shrink files / drop features**; hard-check job-card count (must be **6**).
- Watch for JS pitfalls: `el?.prop = x` (optional-chaining assignment is invalid); a local
  `const t = …` shadows the global i18n `t()` function.
- Confirm **0 console errors** in a real browser (Playwright + Chromium are installed), not static sim.

## 7. How to run / verify
```powershell
python apps/orbis/server/server.py            # → http://127.0.0.1:8765/
python scripts/run_all_tests.py
python scripts/build_site.py ; python scripts/check_site.py ; python scripts/run_all_tests.py
```
Live: https://juwpegatron.github.io/projects/orbis/app.html

## 8. Key reference files
- `apps/orbis/docs/Orbis - Project Home.md`, `apps/orbis/docs/Orbis Master Guide.md`
- `apps/orbis/server/server.py` (the `work` seed + API)
- `apps/orbis/web/app.html` / `app-mobile.html`
- Obsidian vault: `Orbis App/Orbis — Project Home.md` (current); `Loker/` + `Orbis Work/` marked
  **⚠️ SUPERSEDED**.

## 9. Immediate next step
**Finish §4**: implement company pages + employer chat + mass-apply in the `work` view of **both**
`app.html` and `app-mobile.html`, wired to Orbis's bridge; replace/remove the dangling partial
HTML; run the full CI loop; verify in a browser (0 console errors, 6 cards); then push + Obsidian
**only if** everything passes.
