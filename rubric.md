# Org Assessment Rubric

Every observation is scored on **four dimensions**:

## Risk — risk to the org if no action is taken
- **High** — data exposure, data loss, platform limit breach, compliance breach, or integration failure
- **Medium** — reduced functionality, degraded performance, maintainability concern, or missed business objective
- **Low** — minor friction, cosmetic issue, or low-probability future risk

## Value — business or technical gain from implementing the recommendation
- **High** — improves functionality/usability, performance/scalability, or Salesforce roadmap alignment AND decreases complexity
- **Medium** — moderate improvement in one of the above dimensions
- **Low** — marginal gain, nice-to-have

## Effort — degree of effort to implement
- **High** — significant code remediation, data migration, architectural redesign, or broad user-base impact
- **Medium** — moderate code change, config update, or targeted data fix
- **Low** — simple config, field delete, label update, or metadata cleanup

## Priority — derived from Risk × Value
- **High** — High Risk + High Value (act immediately)
- **Medium** — High Risk + Medium Value, or Medium Risk + High Value
- **Low** — Low Risk or Low Value regardless of effort

---

## Rubric Pill Colours (HTML)

| Dimension | High | Medium | Low |
|-----------|------|--------|-----|
| Risk | `bg-red-100 text-red-700 border-red-300` | `bg-orange-100 text-orange-700 border-orange-300` | `bg-green-100 text-green-700 border-green-300` |
| Value | `bg-blue-100 text-blue-700 border-blue-300` | `bg-indigo-100 text-indigo-700 border-indigo-300` | `bg-slate-100 text-slate-500 border-slate-300` |
| Effort | `bg-purple-100 text-purple-700 border-purple-300` | `bg-yellow-100 text-yellow-700 border-yellow-300` | `bg-green-100 text-green-700 border-green-300` |
| Priority | red pill (same as Risk High) | orange pill (same as Risk Medium) | gray pill (same as Value Low) |

---

## Rubric Description Lookup

Hardcode these exact strings in Phase 3 HTML — do not invent new text. Rendered as `text-xs text-slate-500 mt-1 leading-tight` below each pill.

| Dimension | High | Medium | Low |
|-----------|------|--------|-----|
| Risk | Data loss, limit breach, compliance, or integration failure | Degraded performance, reduced functionality, or maintainability concern | Minor friction or low-probability future risk |
| Value | Improves functionality, performance, or roadmap alignment AND reduces complexity | Moderate improvement in one of the above dimensions | Marginal gain or nice-to-have |
| Effort | Architectural redesign, data migration, or broad user-base impact | Moderate code change, config update, or targeted data fix | Simple config, field delete, or metadata cleanup |
| Priority | Act immediately — High Risk + High Value | High Risk + Medium Value, or Medium Risk + High Value | Low Risk or Low Value regardless of effort |

---

## Recommendation Timeline Pills

| Label | Meaning | CSS classes |
|-------|---------|-------------|
| `[ST]` | Short Term | `bg-blue-200 text-blue-800` |
| `[LT]` | Long Term | `bg-purple-200 text-purple-800` |

The full rubric bar uses letter-badge chips (H/M/L) per dimension. Use this exact template:

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
