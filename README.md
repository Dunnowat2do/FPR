# FPRv2.2 — Food Priority Rating Calculator

A single-file, self-contained web app styled after the [NIST/FIRST CVSS calculator](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) — but instead of scoring vulnerability severity, it scores **what you should eat next**.

Pick a level for each metric, and it produces a **base score (0.0–10.0)**, a severity label (None → Critical), and a shareable **vector string** you can copy, paste, and edit.

## What's new

### v2.2
- **Dynamic weighting: Price ↔ Size ↔ Taste.** Weights are no longer fixed numbers — Price and Taste now shift based on what you picked for Size and Price:
  - **Price is inversely proportional to Size.** The heartier the portion, the less Price weighs in the score (down to half its base weight at Hearty). A Light portion leaves Price at full weight.
  - **Expensive + small portion boosts Taste.** If you're paying a premium for a small portion, Taste's weight climbs — up to +60% when Price and Size are both at their most extreme (Expensive and Light together). This only applies when Taste has a real rating; it's skipped when Taste is marked "haven't tried it."
- **Consolidated scoring info.** The little weight badge that used to sit in the corner of every metric card is gone. Instead, a single **"Scoring details"** toggle above the metric list opens a panel listing every metric's current weight (live-updating as you change selections) plus a running total.

### v2.1
- **Copy button.** Replaced the old Randomize button — click it to copy the current vector string to your clipboard (with a manual select-and-copy fallback if clipboard access is blocked).
- **Editable, pasteable vector field.** The vector string is now a live text box. Paste in someone else's vector, or hand-edit the codes, and the buttons and score update as you type.

## Quick start

1. Download `fpr-calculator.html` from this repo.
2. Open it directly in any modern browser — double-click the file, or drag it into a browser window. No build step, no server, no dependencies to install.

To host it instead of opening it locally:
- **GitHub Pages** — commit `fpr-calculator.html` to your repo (rename it to `index.html` if you want it at the root URL), then enable Pages in the repo settings.
- Any static host works too (Netlify, Vercel, S3, etc.) — it's one HTML file with everything inlined.

## How it works

### 1. Rate each metric

The calculator has **7 metrics**, each with 4 levels (Taste has 5 — see below). Click a level to select it; the buttons run low-scoring on the left to high-scoring on the right.

| Metric | Key | Question | Levels (low → high) |
|---|---|---|---|
| Frequency | `FREQ` | How often do you have this? | Regular order → Frequently → Occasionally → Rarely (never tried, or once or twice) |
| Taste | `TASTE` | How good is it, honestly? | *(None — haven't tried it)* / Poor → Decent → Great → Incredible |
| How fast | `FAST` | How long until it reaches your mouth? | Long wait → Slow → Quick → Instant |
| Size | `SIZE` | How filling is the portion? | Light → Modest → Generous → Hearty |
| Price | `PRICE` | What will this cost you? | Expensive → Splurge → Moderate → Cheap |
| Complexity | `CPLX` | How difficult is it to prepare? | Simple (rice + fried elements) → Familiar → Involved → Rarefied (rare ingredients, difficult methods) |
| Appearance | `APPR` | How does it look? | Plain → Decent → Appetizing → Stunning |

### 2. Weighted scoring

Each metric has a base weight:

| Metric | Weight (normal) | Weight (Taste = None) |
|---|---|---|
| Frequency | 2.5 | 2.5 |
| Taste | 5 | — (excluded) |
| Fast | 3 | — (excluded) |
| Size | 3 | 1.5 |
| Price | 2 | 2 |
| Complexity | 2 | 2 |
| Appearance | 2.5 | 6 |

On top of that base table, two rules adjust weights live as you rate the food (see **v2.2** above):
- **Price's weight** shrinks as **Size** increases — hearty portions matter more than the price tag, so Price counts for less.
- **Taste's weight** grows when **Price is high and Size is small at the same time** — overpaying for a tiny portion raises the stakes on whether it actually tastes good.

Click **Scoring details** in the app to see the exact current weight for every metric (and the total) given your current selections, rather than digging through this table.

### 3. The "haven't tried it" special case

If you select **None** for Taste, two things happen automatically:
- The **Fast** metric is grayed out and excluded from scoring, since you can't judge preparation speed for something you've never ordered.
- Weights shift to the "Taste = None" column above — Appearance and Frequency pick up more of the load, and the Price/Taste interaction rule above is skipped entirely (there's no Taste rating for it to boost).

### 4. Read the score

Once every applicable metric is set, the score panel shows:
- **Base score** — a weighted average of the selected levels, 0.0 to 10.0.
- **Severity badge** — same bands as CVSS: **None** (0.0), **Low** (0.1–3.9), **Medium** (4.0–6.9), **High** (7.0–8.9), **Critical** (9.0–10.0).
- **Breakdown table** — every metric's current selection.

### 5. The vector string

Every combination of selections compresses into a vector string, e.g.:

```
FPRv2.2/FREQ:RO/TASTE:I/FAST:Q/SIZE:H/PRICE:C/CPLX:S/APPR:S
```

- **Copy** — click the Copy button to grab the current vector string.
- **Edit or paste** — the vector field is a live text box. Paste in a vector someone sent you, or hand-edit the codes, and the buttons and score update as you type. Unrecognized codes show a small warning underneath instead of breaking anything.

## Customizing

All the content lives in one place: the `metrics` array near the top of the `<script>` block in `fpr-calculator.html`. Each entry defines a metric's key, title, question, and its levels (label, short description, and numeric value 0–10).

Weighting logic lives in two spots just below the `metrics` array:
- `WEIGHTS_NORMAL` and `WEIGHTS_NO_TASTE` — the base weight tables.
- `currentWeights()` — applies the Price/Size/Taste adjustment rules on top of the base tables. Tune the `0.5` (Price-vs-Size falloff) or `0.6` (Taste bonus strength) multipliers here to make the interaction stronger or weaker.

The breakdown table, score, "Scoring details" panel, and vector string are all derived from the same data, so changes in one place propagate everywhere.

## Notes

This is a joke tool, not affiliated with NIST, FIRST, or the U.S. government. It won't help you file a CVE, but it might help you decide between the usual order and something new.
