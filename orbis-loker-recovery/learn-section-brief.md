# Orbis — "Learn" section — design brief (for the builder agent)

## Why / provenance
The user pointed at **harryspeakss (哈利)**'s link-in-bio products and wants an in-app **"Learn"
section inside Orbis** that is **better than both** references:
- **VibeAcademy** (`academy.vibetech.tw/home`): an 8-module "AI-era software fundamentals" path
  with a **"我的學習" /progress study dashboard** (module list, durations, lock/coming-soon states).
- **ailene.id**: AI-learning platform with curriculum + a 5-type AI quiz + progress ("My Progress").
Both share the same idea: a **learning path + a study dashboard**; harryspeakss's page is a
**multi-product hub** (podcast, courses, tools, community) — "different products" in one place.

Build the Orbis version: **a "Learn" hub that combines (a) a multi-product hub and (b) a study
dashboard**, improved over both.

## Where it lives
Inside the **Orbis app** (user's choice): add a new **"Learn"** nav item + `<section id="learn">`
view rendered by `renderLearn()`.
- If an existing view is clearly a learning area (e.g. **Growth**), integrate/extend it instead of
  duplicating, and say so. Otherwise add a distinct **Learn** view.
- Orbis is **server-first** (uses `fetch`). Reuse Orbis's existing view system (`navigateTo` →
  `render<View>()`), CSS classes (`.card`, `.badge`, `.btn`, `.form-label`), and its **indigo/teal**
  look. NO new design system.

## Feature set

### (a) Multi-product hub ("different products", like the linktree)
A row/grid of cards, each an in-app destination or external link, e.g.:
- **Academy** — the learning path (below)
- **AI Type** — jump to the Aura AI-Type test (deep-link to the Aura view)
- **Prompt Library** — ready-to-use prompts (bilingual)
- **Safety Checklist** — pre-send AI safety checks
- **Tools** — e.g. an AI subtitle helper, an AI-English-practice link
- **Community** — Discord/community link
Each card: icon/emoji, title, one-line description, and a clear action.

### (b) Study dashboard (beat VibeAcademy's /progress AND ailene's /dashboard)
Reference — **ailene `/dashboard`** (https://www.ailene.id/dashboard) contains: left nav (Beranda ·
Alur · Pencapaian/achievements · Kuis · Catatan · Prompt Library · Glosarium · Bawa Kerjaanmu · Ide
Latihan · Komunitas · Harga/Live), a greeting **"Halo, Learner."** with "X lessons left to finish
Module N", a **learning path** ("Passport" of 4 levels → modules → lessons with durations + %),
a **current-module card** with its lesson list + "Mulai belajar", a **Streak Belajar** widget (7-day
week strip, current streak, "toward 3 days", record), a **daily tip** ("Tips hari ini"), per-module
**checkpoint quizzes** + a final assessment, **badges**, and a **work-mapping** tool. **VibeAcademy**
has the module list + durations + lock/coming-soon states + a plain progress page.
Build the Orbis version (all of this, but better):
- **Greeting + nudge**: "Halo, [name]." + "X lessons left to finish Module N" (or "start here").
- **Learning path**: levels → modules → lessons. Start with **8 modules** (mirror VibeAcademy's
  software-fundamentals arc, adapted to AI-era skills); each module: title, description, duration,
  lessons, and status `available` / `locked` / `coming-soon` / `complete`.
- **Progress**: overall progress (ring/bar) = completed / total; per-module %; level progress.
- **Study stats strip**: lessons completed · total time · **current streak** · last activity.
- **Streak widget**: a 7-day week strip (highlight today), current streak, goal ("menuju 3 hari"),
  and a record.
- **Continue where you left off** — prominent CTA to the next unfinished lesson.
- **Daily tip** card (bilingual), rotating from a small tip list.
- **Checkpoint quiz** per module (reuse the Aura AI-Type quiz style), plus **badges**
  ("First lesson", "Module 1 done", "3-day streak", "Fundamentals complete").
- **Pockets**: Notes (Catatan), Prompt Library, Glossary (Glosarium) — simple, persisted lists.
- **Practice**: **"Work Map (Peta Kerja AI)"** — see the dedicated brief
  `orbis-loker-recovery/work-map-brief.md` (describe your week → ranked AI-helpable tasks +
  time-saved + copy-ready prompts + recommended lessons, computed locally) — plus Challenges and a
  Community link.
- **Personalized path** — if `state.aura.aiType.primary` exists, show "Recommended for you: start at
  Module N". Personalization only — **never** ranking/matching.
- **Beat-them improvements to make explicit**: fully **bilingual EN⇄ID** (ailene is ID-only,
  VibeAcademy is zh-TW); **works offline + served** (single-file, stdlib backend); **integrated** with
  Orbis (Aura AI-Type + Orbis Work mapping) instead of a silo; privacy-respecting.

### Content seed
- Seed **8 bilingual modules** with a few lessons each (short: title + 2–4 sentence concept + 1
  practical prompt/task). You may **reuse/adapt** `apps/ailene-open/data/curriculum.json` (already
  bilingual EN/ID) as raw material, but present it as Orbis's own path.
- Every string in **both** `id` and `en`. Default = Indonesian.

## State contract (extend, don't break)
```
state.learn = {
  modules: [ { id, title{en,id}, desc{en,id}, duration, status:"available|locked|coming-soon|complete",
               quiz:[ {id, q{en,id}, options:[{en,id}], answer:idx } ],
               lessons:[ { id, title{en,id}, concept{en,id}, prompt{en,id} } ] } ],
  progress: { "<lessonId>": { done:true, at:<epochMs> } },
  quizProgress: { "<moduleId>": { score:n, at:<epochMs> } },
  streak: { count:n, lastDay:"YYYY-MM-DD", record:n, days:["YYYY-MM-DD", ...] },
  badges: [ "<id>" ],
  notes: [ { id, text, at } ], glossarySeen: [ "<termId>" ],
  tipIndex: n, lastActivity: <epochMs>
}
```
Persist via Orbis's existing persist path. If you seed `server.py`, keep its collection API + tests
intact.

## Hard requirements
- Desktop + mobile **parity**; real-browser verify (0 console errors, dashboard renders, completing a
  lesson updates progress, continue-CTA works).
- i18n complete both languages; UTF-8 preserved; files must not shrink; no existing feature dropped.
- Respect Orbis fairness rules (Learn personalization never affects job matching/ranking).

## Must NOT
- Edit `opencode.json`, do NOT `git commit`, do NOT run `opencode` subcommands, do NOT strip
  non-ASCII, do NOT touch the work view's match logic.
