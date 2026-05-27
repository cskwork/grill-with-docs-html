# Mermaid safe subset

Tested against mermaid v10 (CDN) and v11 (mmdc). Stay inside this subset to avoid "Syntax error in text" rendering failures in the browser.

## Hard rules

| Rule | OK | Avoid |
|------|----|-------|
| Wrap every multi-char label in double quotes | `id["Some Label"]` | `id[Some Label]` |
| Edge label with special chars: also wrap in quotes | `A -- "list()" --> B`, `A -. "재시도 정책" .- B` | `A -. 재시도 정책 .- B` (unquoted multi-word) |
| Multi-line label: use `<br/>` inside double quotes | `"Line one<br/>Line two"` | bare newline |
| No pipe `\|` inside any label | `"Retry(delay) or GiveUp"` | `"Retry(delay) \| GiveUp"` |
| No single quote `'` inside double-quoted label | `"10 minutes"` | `"'10 minutes'"` |
| No HTML entities | `"max"` or `"max+"` | `"&gt;= max"` |
| Cylinder `[(…)]` — keep label short, single line | `DB[("twin_image_job")]` | `DB[("twin_image_job<br/>queue")]` |
| Stadium `([…])` / hexagon `{{…}}` — also short labels | `S(["Service"])` | multi-line |
| Edge labels with `()`: quote them | `R -- "list()" --> X` | `R -- list() --> X` |
| Node IDs: ASCII only, no special chars | `S2`, `Repo`, `JobSvc` | `Repo[(...)]` as id |
| Korean text in label: OK inside quotes | `["도메인 single"]` | unquoted Korean with parens |

## Curly braces, brackets, parens inside labels

Safe when wrapped in `["..."]`:

```
W["TwinImageWorker<br/>BACKOFF_SECONDS = 10,30,90"]
P["RetryPolicy<br/>nextAction(attempts, max)"]
```

Risky:

```
P["RetryPolicy<br/>nextAction(attempts, max) -> Retry(delay) | GiveUp"]
```

Above fails because `|` is the shape modifier for edge labels — the parser greedily picks it up even inside quotes in some versions.

## Edge syntax cheat sheet

| Edge | Syntax |
|------|--------|
| Solid arrow | `A --> B` |
| Solid arrow w/ label | `A -- "label" --> B` |
| Dotted arrow | `A -.-> B` |
| Dotted arrow w/ label | `A -. "label" .-> B` |
| Dotted line (no arrow) | `A -.- B` |
| Dotted line (no arrow) w/ label | `A -. "label" .- B` |
| Thick arrow | `A ==> B` |
| Thick arrow w/ label | `A == "label" ==> B` |

## Node shapes

| Shape | Syntax | Notes |
|-------|--------|-------|
| Rectangle (default) | `A["Label"]` | safest |
| Rounded | `A("Label")` | quotes recommended |
| Stadium | `A(["Label"])` | quotes recommended |
| Cylinder (DB) | `A[("Label")]` | keep label simple |
| Diamond | `A{"Label"}` | quotes recommended |
| Hexagon | `A{{"Label"}}` | quotes recommended |
| Circle | `A((Label))` | quotes recommended for non-ASCII |

## Class defs

Use `classDef` for re-using styles. Example:

```
flowchart TB
  X["Shallow"]:::shallow
  Y["Deep"]:::deep
  classDef shallow fill:#fef2f2,stroke:#ef4444
  classDef deep fill:#ecfdf5,stroke:#10b981
```

The `:::shallow` suffix attaches the class. Apply right after the node definition, before any space.

## Korean / multi-language

UTF-8 is fine inside quoted labels. Avoid mixing Korean with special punctuation (slashes, parens) unless quoted.

## Quick checklist before rendering

Before writing the diagram to HTML, scan each block for:

- [ ] Every multi-char label is wrapped in double quotes
- [ ] No `|` inside any quoted label
- [ ] No `'` inside any double-quoted label
- [ ] No `&gt;`, `&lt;`, `&amp;` HTML entities
- [ ] Cylinder shapes only use simple single-line labels
- [ ] Edge labels with `()` are quoted

Then run `mmdc` (see `VALIDATION.md`).
