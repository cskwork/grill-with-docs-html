---
name: grill-with-docs-html
description: Default grilling workflow — same Socratic interview as grill-with-docs (challenges plan against CONTEXT.md / ADRs, sharpens terminology, updates docs inline) but ALWAYS exports the resolved decision tree as an interactive HTML form with FACTS panels, option trade-offs, WHY-recommended panels, Before/After Mermaid diagrams (mmdc-validated), and a sticky "generate decision text" + clipboard copy button. Use whenever the user wants to stress-test a plan, walk a design tree with structured decisions, or pick between candidate refactors. Korean triggers: "그릴링", "디자인 결정", "결정 폼", "후보 그릴링".
---

<what-to-do>

Run the grilling interview exactly as [`grill-with-docs`](../grill-with-docs/SKILL.md) does — Socratic, one question at a time, recommended answer each time, codebase exploration over speculation, inline `CONTEXT.md` / ADR updates as decisions crystallise.

**Default add-on (this skill):** once the interview has produced 3+ structured decisions (typically D1.x / D2.x style grouped under candidates), export the full decision tree as a self-contained HTML form so the user can review the recommendations side-by-side, override any choice, write notes, and copy a final decision text back into chat.

Order of operations:

1. Run the interview (see `../grill-with-docs/SKILL.md`).
2. As decisions land, keep a mental ledger of: candidate id, decision id, options, trade-offs, recommended option, the "why" reasoning, side effects.
3. Draft Before/After Mermaid diagrams for each candidate (current shape vs. deepened shape).
4. **Validate every Mermaid block with mmdc BEFORE writing the HTML.** See `MERMAID-SAFE-SUBSET.md` and `VALIDATION.md`. Do not skip this — rendering errors in the browser block the user.
5. Write the HTML using the template in `HTML-FORM.md` to `$TMPDIR/grill-decisions-<timestamp>.html` (fallback `/tmp/...`) and `open` it.
6. Wait for the user's pasted decision text. Then proceed with implementation.

</what-to-do>

<supporting-info>

## Reuses

This skill reuses the entirety of `grill-with-docs`:

- Domain awareness (CONTEXT.md / CONTEXT-MAP.md discovery, lazy creation) — see `../grill-with-docs/SKILL.md`
- Glossary challenges, fuzzy-term sharpening, scenario stress-tests
- Inline `CONTEXT.md` updates via `../grill-with-docs/CONTEXT-FORMAT.md`
- ADR offers via `../grill-with-docs/ADR-FORMAT.md` (only when hard-to-reverse + surprising + real trade-off)

Read those files for the interview behaviour. This skill's bundled files only cover the HTML export layer.

## HTML form requirements (must all be present)

For each candidate card the HTML must include, in this order:

1. **Header** — badge (Strong / Worth exploring / Speculative), candidate title, file chips with full paths
2. **Before / After Mermaid diagrams** — side-by-side, validated via `mmdc`. See `MERMAID-SAFE-SUBSET.md`.
3. **FACTS panel** (grey, left border) — current code reality: file paths with line numbers, the actual constants / SQL / DTO inventory, the key "signal" that motivated the candidate
4. **Per-decision card** repeated for each Dx.y:
   - decision id + question
   - radio cards, one per option, each with a one-line trade-off underneath
   - Recommended option pre-checked and marked with a green `<span class="recommended">권장</span>` chip
   - **WHY panel** (green, left border) — why the recommended option wins (cite Deletion test, LANGUAGE.md principles, future coupling, etc.)
   - free-text `<textarea>` for the user's per-decision note
5. **SIDE EFFECTS panel** (yellow, left border) at the bottom of the card or the global footer — things that will be touched if these decisions land (CONTEXT.md terms to add, ADR candidates, downstream tests / contracts to update)

Plus a global footer with:
- Order selector (sequencing across candidates)
- Global free-text note textarea
- Sticky bottom bar with **"결정 텍스트 생성"** + **"클립보드로 복사"** buttons and the generated markdown preview

Do NOT collapse FACTS / WHY / trade-offs into the option label. Users explicitly asked for the full reasoning to remain visible.

## Mermaid validation gate

Before writing the HTML, run every diagram through `mmdc`. If any one fails:

1. Re-read `MERMAID-SAFE-SUBSET.md`
2. Simplify the failing diagram (most common culprits: pipe `|` inside labels, single quotes inside double-quoted labels, HTML entities like `&gt;`, cylinder `[(…)]` shape with multi-line labels, missing quotes around edge labels with special characters)
3. Re-run `mmdc`
4. Only after all blocks render to SVG without errors, write the HTML

See `VALIDATION.md` for the exact extract-and-render snippet.

## File location

Write to the OS temp dir so nothing lands in the repo:
- macOS / Linux: `$TMPDIR/grill-decisions-<YYYYMMDD-HHMMSS>.html` (fallback `/tmp`)
- Windows: `%TEMP%\grill-decisions-...`

Open with `open` (macOS), `xdg-open` (Linux), `start` (Windows). Tell the user the absolute path.

## When to skip the HTML export

- The decision tree has fewer than 3 structured decisions (just answer inline)
- The user explicitly asks to keep grilling in chat ("just answer me here", "skip the form")
- No diagram makes sense for the topic (pure naming debates, conventions)

In all other grilling sessions, the HTML export is the default.

</supporting-info>
