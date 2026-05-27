# HTML form template

Self-contained HTML — Tailwind via CDN, Mermaid via CDN, Pretendard font preference for Korean. No build step, no external assets.

## Skeleton

```html
<!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8" />
<title>{{TITLE}}</title>
<script src="https://cdn.tailwindcss.com"></script>
<script type="module">
  import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs";
  mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose", flowchart: { curve: "basis" }, themeVariables: { fontSize: "14px" } });
</script>
<style>
  body { font-family: "Pretendard", ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, sans-serif; }
  .opt-card { transition: background 120ms, border-color 120ms; }
  .opt-card:has(input:checked) { background:#ecfdf5; border-color:#10b981; }
  .opt-card:has(input:checked) .opt-marker { background:#10b981; border-color:#10b981; }
  .opt-card:has(input:checked) .opt-marker::after { opacity:1; }
  .opt-marker { position:relative; width:1rem; height:1rem; border-radius:9999px; border:2px solid #d1d5db; display:inline-block; flex-shrink:0; margin-top:0.2rem; }
  .opt-marker::after { content:""; position:absolute; inset:3px; border-radius:9999px; background:#fff; opacity:0; }
  .recommended { background:#10b98122; color:#065f46; padding:0 6px; border-radius:6px; font-size:0.72rem; font-weight:600; margin-left:0.5rem; }
  .file-chip { font-family: ui-monospace, "JetBrains Mono", "SF Mono", monospace; font-size: 0.74rem; }
  .code { font-family: ui-monospace, "JetBrains Mono", "SF Mono", monospace; }
  .badge-strong { background:#1f2937; color:#fff; }
  .panel-facts { background:#fafaf9; border:1px solid #e7e5e4; border-left:3px solid #57534e; }
  .panel-why { background:#ecfdf5; border:1px solid #a7f3d0; border-left:3px solid #10b981; }
  .panel-note { background:#fffbeb; border:1px solid #fde68a; border-left:3px solid #f59e0b; }
  .panel-context { background:#f5f3ff; border:1px solid #ddd6fe; border-left:3px solid #8b5cf6; }
  .panel-label { font-size:0.65rem; letter-spacing:0.12em; font-weight:700; color:#44403c; }
  .panel-why .panel-label { color:#065f46; }
  .panel-note .panel-label { color:#92400e; }
  .panel-context .panel-label { color:#5b21b6; }
  .trade-off { color:#57534e; font-size:0.82rem; margin-top:0.25rem; }
  .mermaid { background: #fafafa; border: 1px solid #e7e5e4; border-radius: 12px; padding: 0.75rem; font-size: 0.85rem; }
  .diag-label { font-size: 0.7rem; letter-spacing: 0.12em; font-weight: 700; color:#57534e; margin-bottom: 0.35rem; }
  .diag-before .mermaid { border-left: 3px solid #ef4444; background: #fef2f2; }
  .diag-after .mermaid { border-left: 3px solid #10b981; background: #ecfdf5; }
  textarea { font-family: ui-monospace, "JetBrains Mono", "SF Mono", monospace; font-size: 0.82rem; }
  .copy-flash { animation: flashbg 600ms ease; }
  @keyframes flashbg { 0%{background:#10b981; color:#fff} 100%{background:#1f2937; color:#fff} }
  table.facts-table { width:100%; font-size:0.8rem; }
  table.facts-table th, table.facts-table td { border-bottom:1px solid #e7e5e4; padding:0.25rem 0.5rem; text-align:left; vertical-align:top; }
  table.facts-table th { background:#f5f5f4; font-weight:600; }
</style>
</head>
<body class="bg-stone-50 text-stone-900">
<form id="form" class="max-w-5xl mx-auto px-6 py-10">
  <!-- header + domain context panel -->
  <!-- one <section> per candidate (see below) -->
  <!-- order + global note section -->
  <!-- sticky bottom bar with generate + copy -->
</form>
<script>
  // SECTIONS + LABELS + build() + generate/copy handlers (see "JS generator" section below)
</script>
</body>
</html>
```

## Candidate section structure

```html
<section class="mb-12">
  <header class="flex items-center gap-3 mb-2">
    <span class="badge-strong px-3 py-1 rounded-full text-xs font-semibold">{{Strong|Worth exploring|Speculative}} · {{CID}}</span>
    <h2 class="text-2xl font-bold">{{Candidate title}}</h2>
  </header>

  <div class="text-sm flex flex-wrap gap-2 mb-4">
    {{file chips — span.file-chip per relevant file path}}
  </div>

  <!-- Before / After diagrams -->
  <div class="grid md:grid-cols-2 gap-4 mb-5">
    <div class="diag-before">
      <p class="diag-label">BEFORE — {{one-line current pain}}</p>
      <div class="mermaid">{{validated mermaid block}}</div>
    </div>
    <div class="diag-after">
      <p class="diag-label">AFTER — {{one-line target shape}}</p>
      <div class="mermaid">{{validated mermaid block}}</div>
    </div>
  </div>

  <!-- FACTS -->
  <div class="panel-facts p-3 rounded mb-5">
    <p class="panel-label">FACTS — {{subtitle}}</p>
    <ul class="list-disc list-inside text-sm text-stone-700 mt-1 space-y-1">
      {{li per file:line + constant + signal}}
      <li><strong>핵심 신호</strong>: {{the friction that motivated this candidate}}</li>
    </ul>
  </div>

  <!-- Decision card, repeat per Dx.y -->
  <div class="bg-white border border-stone-200 rounded-2xl p-5 mb-4">
    <p class="text-xs uppercase tracking-wider text-stone-500 mb-1">{{Dx.y}} · {{decision label}}</p>
    <h3 class="font-semibold text-lg">{{decision question}}</h3>
    <p class="text-sm text-stone-600 mt-1">{{optional context sentence}}</p>
    <div class="mt-3 space-y-2">
      <!-- one label per option; recommended first with `checked` -->
      <label class="opt-card flex items-start gap-3 border border-stone-200 rounded-xl p-3 cursor-pointer">
        <input type="radio" name="{{name}}" value="{{val}}" checked class="hidden">
        <span class="opt-marker"></span>
        <span>
          <span class="text-sm"><strong>({{tag}})</strong> {{option text}} <span class="recommended">권장</span></span>
          <div class="trade-off">{{one-line trade-off}}</div>
        </span>
      </label>
      <!-- ... -->
    </div>
    <div class="panel-why p-3 rounded mt-3">
      <p class="panel-label">WHY ({{which option}})</p>
      <p class="text-sm text-stone-700 mt-1">{{reasoning — cite Deletion test / LANGUAGE.md principles / coupling}}</p>
    </div>
    <textarea name="{{name}}_note" rows="2" placeholder="메모 (선택)" class="mt-3 w-full border border-stone-200 rounded p-2"></textarea>
  </div>
</section>
```

## Global footer (order + side effects + sticky bar)

```html
<section class="mb-10 bg-white border border-stone-200 rounded-2xl p-5">
  <h3 class="font-semibold text-lg">진행 순서 / 전역 메모</h3>
  <p class="text-sm text-stone-600 mt-1">{{recommended order}}</p>
  <div class="mt-3 space-y-2">
    {{opt-card per order option}}
  </div>
  <div class="panel-note p-3 rounded mt-4">
    <p class="panel-label">SIDE EFFECTS — 결정 확정 시 같이 처리</p>
    <ul class="list-disc list-inside text-sm text-stone-700 mt-1 space-y-1">
      <li>CONTEXT.md 추가 — {{new terms list}}</li>
      <li>ADR 후보 — {{decision ids likely to qualify}}</li>
      <li>{{Other downstream effects: contract tests, dashboard, migrations}}</li>
    </ul>
  </div>
  <label class="block mt-4">
    <span class="text-sm font-semibold">자유 메모 (모두 적용)</span>
    <textarea name="global_note" rows="3" placeholder="..." class="mt-1 w-full border border-stone-200 rounded p-2"></textarea>
  </label>
</section>

<div class="sticky bottom-4 z-10">
  <div class="bg-stone-900 text-stone-50 rounded-2xl p-5 shadow-xl flex flex-col gap-3">
    <div class="flex items-center justify-between gap-4">
      <div>
        <p class="text-xs uppercase tracking-widest text-stone-400">최종 결정 텍스트</p>
        <p class="text-sm text-stone-300">아래 박스를 채워주고 <strong>"클립보드로 복사"</strong>를 누른 뒤 채팅창에 붙여넣어줘.</p>
      </div>
      <div class="flex gap-2">
        <button type="button" id="generate" class="bg-emerald-500 hover:bg-emerald-400 text-white px-4 py-2 rounded-lg font-semibold">결정 텍스트 생성</button>
        <button type="button" id="copy" class="bg-stone-700 hover:bg-stone-600 text-white px-4 py-2 rounded-lg font-semibold">클립보드로 복사</button>
      </div>
    </div>
    <textarea id="output" rows="12" class="w-full bg-stone-950 text-stone-100 rounded-lg p-3 border border-stone-700"></textarea>
  </div>
</div>
```

## JS generator (paste verbatim, only customise LABELS + SECTIONS)

```js
const LABELS = {
  // for each radio group: { value: '사용자에게 보일 라벨' }
  // e.g. d1_1: { a: '(a) ...', b: '(b) ...' },
};

const SECTIONS = [
  // one entry per candidate, items = ordered list of radio group names
  // { key: 'C01 — title', items: ['d1_1','d1_2', ...] },
];

function getValue(name) {
  const r = document.querySelector(`input[name="${name}"]:checked`);
  return r ? r.value : null;
}
function getNote(name) {
  const t = document.querySelector(`textarea[name="${name}_note"]`);
  return t && t.value.trim() ? t.value.trim() : null;
}

function build() {
  const lines = [];
  lines.push('# 결정');
  lines.push('');
  for (const sec of SECTIONS) {
    lines.push('## ' + sec.key);
    for (const id of sec.items) {
      const v = getValue(id);
      const label = (LABELS[id] && LABELS[id][v]) ? LABELS[id][v] : v;
      lines.push(`- **${id.toUpperCase().replace('_','.')}** — ${label}`);
      const note = getNote(id);
      if (note) lines.push(`  - 메모: ${note}`);
    }
    lines.push('');
  }
  lines.push('## 진행 순서');
  const order = getValue('order');
  lines.push('- ' + ((LABELS.order && LABELS.order[order]) || order));
  lines.push('');
  const global = document.querySelector('textarea[name="global_note"]').value.trim();
  if (global) { lines.push('## 전역 메모'); lines.push(global); lines.push(''); }
  lines.push('— 위 결정대로 진행해줘.');
  return lines.join('\n');
}

document.getElementById('generate').addEventListener('click', () => {
  document.getElementById('output').value = build();
});

document.getElementById('copy').addEventListener('click', async () => {
  if (!document.getElementById('output').value.trim()) document.getElementById('output').value = build();
  const text = document.getElementById('output').value;
  try {
    await navigator.clipboard.writeText(text);
    const btn = document.getElementById('copy');
    const orig = btn.textContent;
    btn.textContent = '복사됨 ✓';
    btn.classList.add('copy-flash');
    setTimeout(() => { btn.textContent = orig; btn.classList.remove('copy-flash'); }, 1200);
  } catch (e) {
    document.getElementById('output').select();
    document.execCommand('copy');
  }
});

document.getElementById('generate').click();
```

## Notes

- The CSS `opt-card:has(input:checked)` selector means each radio label colour-fills when picked — no JS needed for that.
- Keep all text in Korean per the user's language rule. Code identifiers (file paths, class names, SQL) stay in original form.
- File is fully self-contained. Open the local HTML directly; the CDN scripts fetch on load.
- Open the file via `open <path>` on macOS, `xdg-open <path>` on Linux, `start <path>` on Windows after writing.
