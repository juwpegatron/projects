# Orbis Aura — "AI Type" test — design brief (for the builder agent)

## Why / provenance
The user saw **ailene.id's "Tes Tipe AI"** (a 12-situation quiz → 5 AI-user types) and wants an
**improved version built INTO Orbis's Aura**, bilingual (EN ⇄ ID), wired to Orbis state.

## ⚠️ Orbis fairness constraint (MUST respect)
Orbis's own rules (in the app) say:
- *"Orbis never infers personality traits."*
- *"Aura never ranks, filters or rejects candidates."*
- *"Match on skills and experience only — never personality."*

Therefore the AI-Type result is a **PRIVATE, opt-in self-discovery tool**:
- Stored only under the user's own Aura (`state.aura.aiType`), **never shared with employers**,
  **never** used to rank/filter/reject anyone, **never** used in job matching.
- The UI must say so (reuse Orbis's privacy-note style).
- Do NOT change the fairness cards or the match logic (match stays skills-only).

## The 5 types (keep the names the user referenced; improve the content)
Each type = a name + Indonesian fauna nickname + one-liner + 3 strengths + 1 "watch-out" +
suggested next step + a suggested **Orbis Aura archetype colour** to set.

| Type | ID fauna nickname | One-liner (EN / ID) | Suggested Aura archetype |
|---|---|---|---|
| **The Professor** | Sang Cendekia (owl) | Understands how AI works before trusting it | Indigo |
| **The Builder** | Sang Perakit (bee) | Learns by making real things with AI | Jade |
| **The Detective** | Sang Penyidik (kancil) | Verifies, cross-checks, hunts hallucinations | Cerulean |
| **The Guardian** | Sang Penjaga (komodo) | Guards data, ethics, and safe use | Stone |
| **The All-Rounder** | Sang Serba-bisa (monyet) | Uses AI across everything; balances speed & care | Teal |

## Test shape (improve over ailene's 12)
- **Aspects (4):** Fondasi AI · Praktik · Evaluasi Kritis · Etika.
- **Scenarios:** **15–18** short situations (more than ailene's 12 for reliability). Each scenario
  has **4–5 options**, each option maps to type weights. Ensure every type is reachable.
- **Scoring:** sum weights → primary type + optional **secondary type** (2nd highest) with a
  "you're mostly X, with a streak of Y" result.
- **Ties:** break by which aspect the user scored highest in; if still tied, default to All-Rounder.
- **Result card:** primary type + secondary + strengths + watch-out + next step + suggested Aura
  archetype + a "share my result" text (copyable) + "use this archetype" button (sets Aura archetype).
- **Progress:** a progress bar + "Situasi N dari M" counter; ~3–4 min.
- No login required; works offline/server-first like the rest of Orbis.

## Where it lives in Orbis
- Inside the **Aura** view (desktop `app.html`; mobile `app-mobile.html`), as a new card/section
  **"AI Type — Kenali cara kerjamu dengan AI"**, placed after the existing Aura form.
- Orbis's indigo/teal look + existing classes (`.card`, `.badge`, `.btn`, `.archetype-swatch`,
  `.form-label`). NO new design system.

## i18n
- ALL new strings must exist in **both** `id` and `en` dictionaries using Orbis's existing i18n key
  mechanism (match how existing keys/`data-i18n` work). Switching language must re-render the quiz
  AND the result instantly. Verify **0 missing keys** in both languages.
- Default language = Indonesian (target market).

## State contract (extend, don't break)
Add under the existing Aura object:
```
aura.aiType = {
  completed: boolean,
  primary: "professor|builder|detective|guardian|allrounder",
  secondary: "<type>|null",
  scores: { professor: n, builder: n, detective: n, guardian: n, allrounder: n },
  completedAt: <epoch ms>,
  lang: "id|en"
}
```
Persist via Orbis's existing persist path (and the server seed should tolerate the new key).

## Hard requirements
- Feature parity desktop + mobile. UTF-8 preserved. Do not shrink files / drop features / 6 role
  cards stay 6. Real-browser verification (0 console errors, quiz completable, result renders).
- Do NOT edit `opencode.json`, do NOT commit, do NOT touch other files.
