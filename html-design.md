# HTML Design System

Mandatory for all generated reports — do not deviate.

---

## Framework & Config

> **CRITICAL:** The `tailwind.config` script block must appear **before** the CDN `<script>` tag, otherwise custom colours (`navy`, `tdblue`, `sky`) will not load on first render.

```html
<script>
tailwind.config = {
  theme: {
    extend: {
      colors: {
        navy:   '#002D62',
        tdblue: '#0051A5',
        sky:    '#E8F2FF',
        border: '#CBD5E0',
      },
      fontFamily: {
        sans: ['Segoe UI', 'system-ui', 'Arial', 'sans-serif'],
        mono: ['Cascadia Code', 'Consolas', 'Courier New', 'monospace'],
      }
    }
  }
}
</script>
<script src="https://cdn.tailwindcss.com"></script>
```

**Font rule (non-negotiable):** No `<link>` to Google Fonts, no `@import url(...)`, no base64 font blobs, no external CDN other than `cdn.tailwindcss.com`.

---

## Page Layout

```
┌──────────────────────────────────────────────────────────────┐
│  HEADER  org name · title · date · prepared-by · platform    │
│  RUBRIC BAR  H/M/L badges per dimension · ST · LT            │
├──────────────────────────────────────────────────────────────┤
│  DOMAIN TAB BAR (sticky, 7 tabs)                             │
│  📋 Summary | 📦 DM | 🔒 SV | 🔗 IA | ⚡ PS | ⚙ CD | 🏛 SG  │
├──────────────┬───────────────────────────────────────────────┤
│  SIDEBAR     │  CONTENT PANEL                                │
│  (w-56,      │  (one panel per sidebar item, flex-1)         │
│  white body, │                                               │
│  navy header)│                                               │
└──────────────┴───────────────────────────────────────────────┘
```

---

## CSS State Classes

```css
.domain-tab { border-bottom: 3px solid transparent; cursor: pointer; }
.domain-tab.active { border-bottom-color: #0051A5; color: #0051A5; font-weight: 600; }
/* !important required — Tailwind utility classes would otherwise override display:none */
.domain-panel { display: none !important; }
.domain-panel.active { display: flex !important; gap: 0; min-height: 100vh; }
/* Sidebar tabs — white body, light-blue selected, navy header strip only */
.sub-tab { border-left: 3px solid transparent; cursor: pointer; transition: background 0.15s; }
.sub-tab:hover { background: #f0f6ff; }
.sub-tab.active { border-left-color: #0051A5; background: #dbeafe; }
.sub-tab.pending { cursor: default; pointer-events: none; }
.sub-panel { display: none !important; }
.sub-panel.active { display: block !important; }
.sum-tab { border-left: 3px solid transparent; cursor: pointer; transition: background 0.15s; }
.sum-tab:hover { background: #f0f6ff; }
.sum-tab.active { border-left-color: #0051A5; background: #dbeafe; }
.sum-panel { display: none !important; }
.sum-panel.active { display: block !important; }
/* Observation card — left accent stripe coloured by priority */
.obs-card { border-left: 4px solid #CBD5E0; border-radius: 0.75rem; overflow: hidden; margin-bottom: 1.25rem; box-shadow: 0 1px 4px rgba(0,0,0,0.07); background: #fff; }
.obs-card.priority-high   { border-left-color: #ef4444; }
.obs-card.priority-medium { border-left-color: #f97316; }
.obs-card.priority-low    { border-left-color: #94a3b8; }
.obs-header { background: #f8fafc; border-bottom: 1px solid #e2e8f0; padding: 0.875rem 1.25rem; display: flex; align-items: flex-start; gap: 0.75rem; }
@media print { .no-print { display: none; } }
```

---

## Tab JavaScript

All functions are **required** in every report. Place in a single `<script>` block just before `</body>`.

```js
function showDomain(key) {
  document.querySelectorAll('.domain-tab').forEach(t =>
    t.classList.toggle('active', t.dataset.domain === key));
  document.querySelectorAll('.domain-panel').forEach(p =>
    p.classList.toggle('active', p.id === 'panel-' + key));
}
function showSub(id) {
  document.querySelectorAll('.sub-tab').forEach(t =>
    t.classList.toggle('active', t.dataset.sub === id));
  document.querySelectorAll('.sub-panel').forEach(p =>
    p.classList.toggle('active', p.id === 'sub-' + id));
}
function showSum(id) {
  document.querySelectorAll('.sum-tab').forEach(t =>
    t.classList.toggle('active', t.dataset.sum === id));
  document.querySelectorAll('.sum-panel').forEach(p =>
    p.classList.toggle('active', p.id === 'sum-' + id));
}
function goToSub(domainKey, subKey) {
  showDomain(domainKey);
  showSub(subKey);
  setTimeout(function() {
    var el = document.getElementById('sub-' + subKey);
    if (el) el.scrollIntoView({ behavior: 'smooth', block: 'start' });
  }, 80);
}
function goToObs(domainKey, subKey, obsId) {
  showDomain(domainKey);
  showSub(subKey);
  setTimeout(function() {
    var el = document.getElementById('obs-' + obsId);
    if (el) {
      el.scrollIntoView({ behavior: 'smooth', block: 'center' });
      el.style.outline = '2px solid #0051A5';
      setTimeout(function() { el.style.outline = ''; }, 1800);
    }
  }, 80);
}
/* REQUIRED: initialise default state after DOM is ready */
document.addEventListener('DOMContentLoaded', function() {
  showDomain('summary');
  showSum('intro');
});
```

`domainKey` = lowercase domain code (`'dm'`, `'sv'`, `'ia'`, `'ps'`, `'cd'`, `'sg'`).
`subKey` = lowercase hyphenated sub-domain code (`'dm-01'`, `'ps-02'`, etc.).

> **KEY RULE — showSum:** `showSum(id)` looks up `id="sum-" + id` — so `data-sum` and `onclick="showSum(…)"` must use the **short key** (e.g. `intro`), while the panel `id` uses the full prefixed value (e.g. `sum-intro`). Using `showSum('sum-intro')` causes a double-prefix bug and renders a blank panel.

> **KEY RULE — domain panels:** Never put the Tailwind `flex` class directly on a `.domain-panel` div. The CSS `!important` rules control display. The `flex` layout is already provided by `.domain-panel.active { display: flex !important; }`.

---

## Header HTML Template

```html
<!-- HEADER -->
<div style="background:#002D62;" class="px-6 py-5 text-white">
  <div class="max-w-screen-2xl mx-auto">
    <div class="flex items-start justify-between flex-wrap gap-3">
      <div>
        <div class="text-xs font-semibold text-blue-200 uppercase tracking-widest mb-1">Salesforce Org Assessment</div>
        <h1 class="text-2xl font-bold leading-tight">[Customer / Org Name]</h1>
        <p class="text-blue-100 text-sm mt-0.5">Technical Assessment Report</p>
      </div>
      <div class="text-right text-xs text-blue-200 space-y-0.5">
        <div>Assessment date: <span class="text-white font-semibold">YYYY-MM-DD</span></div>
        <div>Prepared by: <span class="text-white font-semibold">Salesforce Advisory</span></div>
      </div>
    </div>
    <!-- Platform product pills -->
    <div class="mt-3 flex flex-wrap gap-2">
      <span class="bg-blue-700 text-blue-100 text-xs px-2.5 py-1 rounded-full font-medium">Sales Cloud</span>
      <!-- repeat per platform product -->
    </div>
  </div>
</div>
```

---

## Rubric Bar HTML Template

```html
<!-- RUBRIC BAR -->
<div class="bg-white border-b border-slate-200 px-6 py-2">
  <div class="max-w-screen-2xl mx-auto flex flex-wrap items-center gap-x-1 gap-y-1.5 text-xs text-slate-600">
    <span class="font-bold text-slate-500 uppercase tracking-wide text-xs mr-2">RUBRIC:</span>
    <!-- Risk -->
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#ef4444;">H</span><span class="text-slate-600">Risk High</span></span>
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#f97316;">M</span><span class="text-slate-600">Risk Med</span></span>
    <span class="flex items-center gap-1 mr-3"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#22c55e;">L</span><span class="text-slate-600">Risk Low</span></span>
    <span class="w-px h-4 bg-slate-200 mr-3"></span>
    <!-- Value -->
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#3b82f6;">H</span><span class="text-slate-600">Value High</span></span>
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#6366f1;">M</span><span class="text-slate-600">Value Med</span></span>
    <span class="flex items-center gap-1 mr-3"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#94a3b8;">L</span><span class="text-slate-600">Value Low</span></span>
    <span class="w-px h-4 bg-slate-200 mr-3"></span>
    <!-- Effort -->
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#a855f7;">H</span><span class="text-slate-600">Effort High</span></span>
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#eab308;">M</span><span class="text-slate-600">Effort Med</span></span>
    <span class="flex items-center gap-1 mr-3"><span class="inline-flex items-center justify-center w-5 h-5 rounded text-white font-bold text-xs" style="background:#22c55e;">L</span><span class="text-slate-600">Effort Low</span></span>
    <span class="w-px h-4 bg-slate-200 mr-3"></span>
    <!-- Timeline -->
    <span class="flex items-center gap-1 mr-1"><span class="inline-flex items-center justify-center px-1.5 h-5 rounded font-bold text-xs" style="background:#bfdbfe;color:#1e40af;">ST</span><span class="text-slate-600">Short Term</span></span>
    <span class="flex items-center gap-1"><span class="inline-flex items-center justify-center px-1.5 h-5 rounded font-bold text-xs" style="background:#e9d5ff;color:#6b21a8;">LT</span><span class="text-slate-600">Long Term</span></span>
  </div>
</div>
```

---

## Domain Tab Bar HTML Template

```html
<!-- DOMAIN TAB BAR -->
<div class="bg-white border-b border-slate-200 sticky top-0 z-30 no-print">
  <div class="max-w-screen-2xl mx-auto px-4">
    <div class="flex gap-0 overflow-x-auto">
      <div class="domain-tab active px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="summary" onclick="showDomain('summary')">📋 Summary</div>
      <div class="domain-tab px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="dm" onclick="showDomain('dm')">📦 Data Management</div>
      <div class="domain-tab px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="sv" onclick="showDomain('sv')">🔒 Security &amp; Visibility</div>
      <div class="domain-tab px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="ia" onclick="showDomain('ia')">🔗 Integration Architecture</div>
      <div class="domain-tab px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="ps" onclick="showDomain('ps')">⚡ Performance &amp; Scalability</div>
      <div class="domain-tab px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="cd" onclick="showDomain('cd')">⚙️ Configuration &amp; Development</div>
      <div class="domain-tab px-3 py-3 text-xs text-slate-600 whitespace-nowrap" data-domain="sg" onclick="showDomain('sg')">🏛 Strategy &amp; Governance</div>
    </div>
  </div>
</div>
```

---

## Sidebar HTML Template

**White body, navy header strip, light-blue active row.** The sidebar is `w-56`, sticky, with a `border border-slate-200 bg-white` container and a `style="background:#002D62;"` header strip only.

```html
<!-- Domain sidebar (e.g. DM) -->
<div class="w-56 shrink-0 rounded-xl shadow-sm mr-5 self-start sticky top-16 no-print overflow-hidden border border-slate-200 bg-white">
  <div class="px-4 py-3 text-xs font-bold text-white uppercase tracking-widest" style="background:#002D62;">📦 Data Management</div>
  <div class="py-1">
    <!-- Active / assessed sub-domain (no version shown) -->
    <div class="sub-tab active px-4 py-3 text-xs" data-sub="dm-01" onclick="showSub('dm-01')">
      <span class="font-mono font-bold text-slate-700 text-xs">DM-01</span>
      <span class="block text-slate-600 text-xs mt-0.5 font-normal">Object Fields</span>
    </div>
    <!-- Assessed, not currently active -->
    <div class="sub-tab px-4 py-3 text-xs" data-sub="dm-02" onclick="showSub('dm-02')">
      <span class="font-mono font-bold text-slate-600 text-xs">DM-02</span>
      <span class="block text-slate-500 text-xs mt-0.5 font-normal">Record Types, Layouts, VRs</span>
    </div>
    <!-- Not yet assessed -->
    <div class="sub-tab pending px-4 py-3 text-xs" data-sub="dm-03" onclick="showSub('dm-03')">
      <span class="font-mono font-bold text-slate-400 text-xs">DM-03</span>
      <span class="block text-slate-400 text-xs mt-0.5 italic">— Coming Soon</span>
    </div>
  </div>
</div>
```

Summary sidebar uses `sum-tab` / `data-sum` / `showSum()` with short keys (`intro`, `domains`, `priority`, `ranked`, `roadmap`) — same structural pattern.

---

## Observation Card — Full HTML Template

Each card uses `.obs-card` + a priority modifier class. The left border stripe colour is automatic from CSS.

```html
<div id="obs-DM-01.1" class="obs-card priority-high">

  <!-- Header row: grey background, observation text -->
  <div class="obs-header">
    <span class="rounded bg-slate-200 text-slate-600 text-xs font-bold font-mono px-1.5 py-0.5 shrink-0 mt-0.5">DM-01.1</span>
    <p class="text-sm text-slate-800 leading-snug">One-sentence observation naming the specific artifact.</p>
  </div>

  <!-- Impact / Recommendation row -->
  <div class="grid grid-cols-1 md:grid-cols-2 border-b border-slate-100">
    <div class="px-5 py-3.5 border-r border-slate-100">
      <div class="text-xs font-semibold text-red-700 mb-1.5 flex items-center gap-1.5">🔴 Impact</div>
      <p class="text-xs text-slate-600 leading-relaxed">One sentence. What fails, degrades, or costs money.</p>
    </div>
    <div class="px-5 py-3.5">
      <!-- ST badge: bg-blue-200 text-blue-800 | LT badge: bg-purple-200 text-purple-800 -->
      <div class="text-xs font-semibold text-blue-700 mb-1.5 flex items-center gap-1.5">🔵 Recommendation
        <span class="bg-blue-200 text-blue-800 px-1.5 py-0.5 rounded text-xs font-mono font-bold ml-1">ST</span>
      </div>
      <p class="text-xs text-slate-600 leading-relaxed">One sentence. Specific action tied to the named artifact.</p>
    </div>
  </div>

  <!-- Rubric row: 4 equal columns -->
  <div class="grid grid-cols-4 divide-x divide-slate-100">
    <div class="px-4 py-3">
      <div class="text-xs text-slate-400 uppercase tracking-wide font-semibold mb-1.5">Risk</div>
      <span class="inline-flex items-center bg-red-100 text-red-700 border border-red-300 rounded px-2 py-0.5 text-xs font-semibold">High</span>
      <p class="text-xs text-slate-500 mt-1 leading-tight">Data loss, limit breach, compliance, or integration failure</p>
    </div>
    <div class="px-4 py-3">
      <div class="text-xs text-slate-400 uppercase tracking-wide font-semibold mb-1.5">Value</div>
      <!-- High: bg-blue-100 text-blue-700 border-blue-300 -->
      <!-- Medium: bg-indigo-100 text-indigo-700 border-indigo-300 -->
      <!-- Low: bg-slate-100 text-slate-500 border-slate-300 -->
      <span class="inline-flex items-center bg-blue-100 text-blue-700 border border-blue-300 rounded px-2 py-0.5 text-xs font-semibold">High</span>
      <p class="text-xs text-slate-500 mt-1 leading-tight">Improves functionality, performance, or roadmap alignment AND reduces complexity</p>
    </div>
    <div class="px-4 py-3">
      <div class="text-xs text-slate-400 uppercase tracking-wide font-semibold mb-1.5">Effort</div>
      <!-- High: bg-purple-100 text-purple-700 border-purple-300 -->
      <!-- Medium: bg-yellow-100 text-yellow-700 border-yellow-300 -->
      <!-- Low: bg-green-100 text-green-700 border-green-300 -->
      <span class="inline-flex items-center bg-yellow-100 text-yellow-700 border border-yellow-300 rounded px-2 py-0.5 text-xs font-semibold">Medium</span>
      <p class="text-xs text-slate-500 mt-1 leading-tight">Moderate code change, config update, or targeted data fix</p>
    </div>
    <div class="px-4 py-3">
      <div class="text-xs text-slate-400 uppercase tracking-wide font-semibold mb-1.5">Priority</div>
      <!-- High: bg-red-100 text-red-700 border-red-300 font-bold -->
      <!-- Medium: bg-orange-100 text-orange-700 border-orange-300 font-bold -->
      <!-- Low: bg-gray-100 text-gray-600 border-gray-300 font-bold -->
      <span class="inline-flex items-center bg-red-100 text-red-700 border border-red-300 rounded px-2 py-0.5 text-xs font-bold">High</span>
      <p class="text-xs text-slate-500 mt-1 leading-tight">Act immediately — High Risk + High Value</p>
    </div>
  </div>

</div>
```

**Pill colour quick reference:**

| Dimension | High | Medium | Low |
|-----------|------|--------|-----|
| Risk | `bg-red-100 text-red-700 border-red-300` | `bg-orange-100 text-orange-700 border-orange-300` | `bg-green-100 text-green-700 border-green-300` |
| Value | `bg-blue-100 text-blue-700 border-blue-300` | `bg-indigo-100 text-indigo-700 border-indigo-300` | `bg-slate-100 text-slate-500 border-slate-300` |
| Effort | `bg-purple-100 text-purple-700 border-purple-300` | `bg-yellow-100 text-yellow-700 border-yellow-300` | `bg-green-100 text-green-700 border-green-300` |
| Priority | `bg-red-100 text-red-700 border-red-300` | `bg-orange-100 text-orange-700 border-orange-300` | `bg-gray-100 text-gray-600 border-gray-300` |

---

## Summary Tab

Always the first tab and default landing tab. Same sidebar structure as domain panels.

### Sidebar items

| `data-sum` (short key) | Panel `id` | Label | Content |
|---|---|---|---|
| `intro` | `sum-intro` | 📖 Introduction | Purpose · Methodology · Analysis Tools · Org stats card · Areas of Review grid |
| `domains` | `sum-domains` | 📦 Findings by Domain | Table per assessed domain — obs counts, High/Medium/Low breakdown |
| `priority` | `sum-priority` | ⭐ Highest Priority | Three sections: High Value·Low Effort → Medium Effort → High Effort |
| `ranked` | `sum-ranked` | 📊 All Findings Ranked | Full table sorted Priority → Value → Effort |
| `roadmap` | `sum-roadmap` | 🗺 Optimization Roadmap | Three-column board: Stabilize Now · Optimize Next · Transform |

Refreshed on every Phase 3 run — re-derive all counts and ranked lists from current approved markdown files.

### Introduction — Areas of Review grid

```html
<div class="mt-8 pt-6 border-t border-slate-200">
  <h3 class="text-xs font-bold text-tdblue uppercase tracking-wide mb-4">Areas of Review</h3>
  <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
    <div class="bg-white border border-slate-200 rounded-xl overflow-hidden shadow-sm">
      <div class="px-4 py-2.5" style="background:#002D62;">
        <span class="text-white font-bold text-xs">📦 Data Management</span>
      </div>
      <ul class="px-4 py-3 space-y-1.5">
        <!-- Assessed sub-domain — clickable -->
        <li class="flex items-center gap-2 text-xs">
          <button onclick="goToSub('dm','dm-01')" class="font-mono font-bold text-tdblue hover:underline shrink-0">DM-01</button>
          <span class="text-slate-600">Object Fields</span>
        </li>
        <!-- Pending sub-domain — not clickable -->
        <li class="flex items-center gap-2 text-xs">
          <span class="font-mono font-bold text-slate-400 shrink-0">DM-02</span>
          <span class="text-slate-400">Record Types, Page Layouts, Validation Rules</span>
        </li>
      </ul>
    </div>
    <!-- repeat for all 6 domains -->
  </div>
  <p class="text-xs text-slate-400 mt-3 italic">Assessed sub-domains shown in <span class="text-tdblue font-bold">blue</span> are clickable. Pending shown in grey.</p>
</div>
```

### Findings by Domain table — sub-domain deep-link cell

```html
<button onclick="goToSub('dm','dm-01')" class="text-left">
  <span class="font-mono font-bold text-tdblue hover:underline">DM-01</span>
  <span class="block text-xs text-slate-500 mt-0.5">Object Fields</span>
</button>
```

### Highest Priority / All Findings Ranked — observation deep-link

```html
<button onclick="goToObs('dm','dm-01','DM-01.2')" class="flex items-center gap-1.5 text-left group">
  <span class="rounded bg-slate-200 text-slate-600 text-xs font-bold font-mono px-1.5 py-0.5 group-hover:bg-tdblue group-hover:text-white transition-colors">DM-01.2</span>
  <span class="bg-sky-50 text-tdblue border border-blue-200 rounded text-xs px-1.5 py-0.5 whitespace-nowrap">📦 DM · Object Fields</span>
</button>
```

### Optimization Roadmap — three-column board

| Column | Header colour | CSS | Contents |
|--------|--------------|-----|----------|
| 🚨 Stabilize Now | Rose | `bg-rose-600` header, `bg-rose-50` body | High Risk findings |
| 🔧 Optimize Next | Amber | `bg-amber-500` header, `bg-amber-50` body | Medium priority improvements |
| 🌱 Transform | Green | `bg-green-600` header, `bg-green-50` body | Strategic / long-term improvements |

---

## Offline Safety Checklist

Run before outputting — if any found, remove and regenerate:

- `fonts.googleapis.com`
- `fonts.gstatic.com`
- `base64`
- `@import url`
- Any `<link rel="stylesheet">` other than the Tailwind CDN
- Tailwind `flex` class on a `.domain-panel` div (use CSS class only)

Target file size: **under 800 KB**.
