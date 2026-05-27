# Mermaid validation gate

**Required step before writing the HTML.** Every `<div class="mermaid">…</div>` block must render to SVG without errors via `mmdc`.

## Prerequisites

- Node.js with `npx` available
- `@mermaid-js/mermaid-cli` (auto-installed by `npx --yes -p @mermaid-js/mermaid-cli mmdc`)
- macOS / Linux with bash

## Extract + render snippet

Run after drafting all diagrams, before writing the HTML to its final temp path.

```bash
# 1) Stage the in-progress HTML to a temp draft path
DRAFT=/tmp/grill-decisions-draft.html
# (write your draft HTML there first)

# 2) Extract every mermaid block
python3 - <<'PY'
import re, os
src = open('/tmp/grill-decisions-draft.html').read()
blocks = re.findall(r'<div class="mermaid">\n(.*?)\n\s*</div>', src, re.DOTALL)
out = '/tmp/mermaid-validate'
os.makedirs(out, exist_ok=True)
for i, b in enumerate(blocks, 1):
    open(f'{out}/d{i:02d}.mmd', 'w').write(b.strip() + '\n')
print(f'extracted {len(blocks)} blocks')
PY

# 3) Render each block; mmdc exits non-zero on syntax errors
cd /tmp/mermaid-validate && for f in d*.mmd; do
  echo "=== $f ==="
  npx --yes -p @mermaid-js/mermaid-cli mmdc -q -i "$f" -o "${f%.mmd}.svg" 2>&1 | grep -Ei 'error|syntax|parse|warn' | head -5
done

# 4) Confirm SVG output exists for every input
ls /tmp/mermaid-validate/*.svg 2>&1 | wc -l   # should equal extracted block count
```

## Pass criteria

- Step 3 prints `=== dNN.mmd ===` with **no** lines matching `error|syntax|parse|warn`
- Step 4 SVG count equals the number of extracted blocks
- Only after both, finalise the HTML write to its real `grill-decisions-<timestamp>.html` path and `open` it

## Failure recovery

If any diagram fails:

1. Read the full stderr by re-running without the grep:
   ```bash
   npx --yes -p @mermaid-js/mermaid-cli mmdc -i /tmp/mermaid-validate/d03.mmd -o /tmp/d03.svg
   ```
2. Cross-check against `MERMAID-SAFE-SUBSET.md`. The vast majority of errors trace to one of:
   - unquoted multi-word edge label
   - `|` in label
   - `'` inside `"…"`
   - `&gt;` instead of literal text
   - cylinder with multi-line label
3. Fix the diagram in your draft, re-run extract+render
4. Loop until all pass

## When to skip

Skip the validation step ONLY when no diagrams were drafted (rare — the skill assumes Before/After per candidate). In that case still log "no diagrams to validate" so the omission is intentional, not forgotten.

## Performance note

`mmdc` cold-starts Chromium via Puppeteer (~5–10s). Subsequent renders in the same session reuse the install. For 6 diagrams, expect ~20–40s wall time.
