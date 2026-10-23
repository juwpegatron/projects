# Orbis "Learn" → Practice → **Work Map (Peta Kerja AI)** — design brief

## Why / provenance
The user asked to `/rea` **ailene's "Peta Kerja AI"** (work-map): <https://www.ailene.id/peta-kerja>
(and a result page `.../peta-kerja/<uuid>`). REA = reverse-engineer → improve (never clone).
The result page is a **JS-rendered SPA behind a session**, so its exact output couldn't be read
directly; the flow below is from the live input page + ailene's own description. Build an **improved
version inside Orbis's Learn section** (Practice area).

## What ailene's Peta Kerja does (the flow to beat)
1. Pick your field (Admin/UMKM, Sales, Marketing, Finance, Educator, Course creator, Design,
   Developer/IT, Freelancer, Other).
2. **Describe your week**: what you do, how often (daily/weekly/monthly), what eats the most time
   (min ~80 chars; warns against pasting confidential data).
3. Optional: tools you use (Excel/Sheets, Word/Docs, PowerPoint/Slides, Canva, WhatsApp, Email,
   Instagram, Notion).
→ Ailene returns: the tasks AI can most help with, **estimated time saved**, **copy-ready prompts**,
and **recommended materials/lessons**. Example: "1. Balas chat WA pelanggan (±2 jam) · 2. Rekap
laporan mingguan (±1 jam) + prompt siap copy & materinya."

## Build it better (Orbis version)
Orbis is **offline-first + stdlib-only** (no LLM backend). So the engine must be **local,
deterministic, explainable** — no data leaves the device (a real privacy win over a server LLM).

### Input (bilingual EN⇄ID)
- Field dropdown + free-text work-week description + tools multi-select, with a clear
  **"don't paste confidential data"** note.
- Add: frequency + "biggest time sink" quick-picks (to improve matching), and a "meeting-heavy?" toggle.

### Analysis (rules/heuristic engine, offline)
- A **task library**: ~40–60 known AI-helpable work tasks, each tagged by: field(s), tool(s), trigger
  keywords (EN+ID), typical **hours saved/week**, a **copy-ready prompt** (EN+ID), and the
  **recommended Learn module id**.
- Score = field match + tool match + keyword match + frequency weight → rank.
- Always show **WHY** each suggestion fired (which keyword/tool matched) — explainable, not a black box.
- No match → say so and suggest the closest generic tasks (don't invent).

### Output
- Ranked list of AI-helpable tasks, each with: title, **≈ hours/week saved**, tools, a **prompt you
  can copy**, and a **deep-link to the matching Learn module**.
- **Header total**: "≈ X hours/week you could reclaim".
- **Adoption checklist**: tick tasks you've tried (persisted).
- **Save / multiple maps**, **edit**, and **export** (copy as Markdown/text). Re-run anytime.
- **Privacy line**: "Everything is computed on your device; nothing is sent anywhere."

### Integration
- Sits under **Learn → Practice** (alongside "Challenges" + "Community").
- Deep-links into Learn modules (recommended material) and can show Orbis Work context
  (your roles/applications) if present. Aura AI-Type personalization may reorder suggestions (never
  affects job matching).

## State contract
```
state.learn.workMaps = [ {
  id, createdAt, field, description, tools:[...], frequency, biggestTimeSink, meetingHeavy:bool,
  results: [ { taskId, score, why, hoursSaved, prompt{en,id}, moduleId } ],
  adopted: [ "<taskId>" ], totalHoursSaved: n
} ]
```
Persist via Orbis's existing persist path.

## Hard requirements
- Desktop + mobile parity; real-browser verify (complete the flow offline → ranked results render,
  copy-prompt works, deep-link opens the Learn module, 0 console errors).
- i18n complete both languages; UTF-8 preserved; no shrink; no feature dropped.
- Respect Orbis fairness/privacy rules.
- Must NOT: edit `opencode.json`, `git commit`, run `opencode` subcommands, strip non-ASCII.
