---
name: grill-with-docs-html
description: Grilling session that walks the design decision tree Socratically and exports it as a self-contained interactive HTML decision form. Use when the user wants to stress-test a plan, compare candidate refactors, or wants a grill-with-docs session exported as a reviewable, overridable form. Korean triggers include 그릴링, 디자인 결정, 결정 폼, 후보 그릴링.
---

<what-to-do>

Run the grilling interview exactly as [`grill-with-docs`](../grill-with-docs/SKILL.md) does — Socratic, one question at a time, recommended answer each time, codebase exploration over speculation, inline `CONTEXT.md` / ADR updates as decisions crystallise.

**Default add-on (this skill):** once the interview has produced 3+ structured decisions (typically D1.x / D2.x style grouped under candidates), export the full decision tree as a self-contained HTML form so the user can review the recommendations side-by-side, override any choice, write notes, and copy a final decision text back into chat.

Order of operations:

1. Run the interview (see `../grill-with-docs/SKILL.md`).
2. As decisions land, keep a mental ledger of: candidate id, decision id, options, trade-offs, recommended option, the "why" reasoning, side effects.
3. Draft Before/After Mermaid diagrams for each candidate (current shape vs. deepened shape) — read `MERMAID-SAFE-SUBSET.md` before writing the first diagram.
4. **Validate every Mermaid block with mmdc BEFORE writing the HTML.** Read `VALIDATION.md` for the extract-and-render snippet and run it — an unrendered block shows the user a syntax error instead of a diagram.
5. Write the HTML using the template in `HTML-FORM.md` to `$TMPDIR/grill-decisions-<YYYYMMDD-HHMMSS>.html` (fallback `/tmp`), then open it (see **File location**).
6. Wait for the user's pasted decision text. Then proceed with implementation.

</what-to-do>

<supporting-info>

## Reuses

This skill reuses the entirety of `grill-with-docs`:

- Domain awareness (CONTEXT.md / CONTEXT-MAP.md discovery, lazy creation) — see `../grill-with-docs/SKILL.md`
- Glossary challenges, fuzzy-term sharpening, scenario stress-tests
- Inline `CONTEXT.md` updates via `../grill-with-docs/CONTEXT-FORMAT.md`
- ADR offers via `../grill-with-docs/ADR-FORMAT.md` (only when hard-to-reverse + surprising + real trade-off)

Read those files for the interview behaviour; this skill's bundled files only cover the HTML export layer. If `../grill-with-docs/` is absent, tell the user it is missing before starting — the interview behaviour lives there, not here.

## HTML form requirements (must all be present)

For each candidate card the HTML must include, in this order:

1. **Header** — badge (Strong / Worth exploring / Speculative), candidate title, file chips with full paths
2. **Before / After Mermaid diagrams** — side-by-side, validated via `mmdc`
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

Keep FACTS, WHY, and per-option trade-offs in their own panels — the option label carries the option text and nothing else. Users asked for the full reasoning to stay visible.

## When a diagram fails mmdc

Re-read `MERMAID-SAFE-SUBSET.md` for the offending pattern, simplify that diagram, re-run `mmdc`, and loop until every block renders to SVG clean.

## File location

Write to the OS temp dir so nothing lands in the repo — `$TMPDIR` on macOS / Linux (fallback `/tmp`), `%TEMP%` on Windows. Open with `open` (macOS), `xdg-open` (Linux), `start` (Windows). Tell the user the absolute path.

## When to skip the HTML export

- The decision tree has fewer than 3 structured decisions (just answer inline)
- The user explicitly asks to keep grilling in chat ("just answer me here", "skip the form")
- No diagram makes sense for the topic (pure naming debates, conventions)

In all other grilling sessions, the HTML export is the default.

</supporting-info>
