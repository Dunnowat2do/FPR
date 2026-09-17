# FPRv2.1 — Food Priority Rating Calculator

A single-file, self-contained web app styled after the [NIST/FIRST CVSS calculator](https://nvd.nist.gov/vuln-metrics/cvss/v3-calculator) — but instead of scoring vulnerability severity, it scores **what you should eat next**.

Pick a level for each metric, and it produces a **base score (0.0–10.0)**, a severity label (None → Critical), and a shareable **vector string** you can copy, paste, and edit.

## Quick start

1. Download `fpr-calculator.html` from this repo.
2. Open it directly in any modern browser — double-click the file, or drag it into a browser window. No build step, no server, no dependencies to install.

To host it instead of opening it locally:
- **GitHub Pages** — commit `fpr-calculator.html` to your repo (rename it to `index.html` if you want it at the root URL), then enable Pages in the repo settings.
- Any static host works too (Netlify, Vercel, S3, etc.) — it's one HTML file with everything inlined.

## How it works

### 1. Rate each metric

The calculator has **7 metrics**, each with 4 levels (Taste has 5 — see below). Click a level to select it; the buttons run low-scoring on the left to high-scoring on the right.

| Metric | Key | Question | Levels (low → high) | Weight |
|---|---|---|---|---|
| Frequency | `FREQ` | How often do you have this? | Regular order → Frequently → Occasionally → Rarely (never tried, or once or twice) | 2.5 |
| Taste | `TASTE` | How good is it, honestly? | *(None — haven't tried it)* / Poor → Decent → Great → Incredible | 5 |
| How fast | `FAST` | How long until it reaches your mouth? | Long wait → Slow → Quick → Instant | 3 |
| Size | `SIZE` | How filling is the portion? | Light → Modest → Generous → Hearty | 3 |
| Price | `PRICE` | What will this cost you? | Expensive → Splurge → Moderate → Cheap | 2 |
| Complexity | `CPLX` | How difficult is it to prepare? | Simple (rice + fried elements) → Familiar → Involved → Rarefied (rare ingredients, difficult methods) | 2 |
| Appearance | `APPR` | How does it look? | Plain → Decent → Appetizing → Stunning | 2.5 |

### 2. The "haven't tried it" special case

If you select **None** for Taste (meaning you've never actually tasted this dish), two things happen automatically:
- The **Fast** metric is grayed out and excluded from scoring, since you can't judge preparation speed for something you've never ordered.
- The weights shift to account for the two missing metrics:

| Metric | Weight (normal) | Weight (Taste = None) |
|---|---|---|
| Frequency | 2.5 | 2.5 |
| Taste | 5 | — (excluded) |
| Fast | 3 | — (excluded) |
| Size | 3 | 1.5 |
| Price | 2 | 2 |
| Complexity | 2 | 2 |
| Appearance | 2.5 | 6 |

### 3. Read the score

Once every applicable metric is set, the score panel shows:
- **Base score** — a weighted average of the selected levels, 0.0 to 10.0.
- **Severity badge** — same bands as CVSS: **None** (0.0), **Low** (0.1–3.9), **Medium** (4.0–6.9), **High** (7.0–8.9), **Critical** (9.0–10.0).
- **Breakdown table** — every metric's current selection.

### 4. The vector string

Every combination of selections compresses into a vector string, e.g.:

```
FPRv2.1/FREQ:RO/TASTE:I/FAST:Q/SIZE:H/PRICE:C/CPLX:S/APPR:S
```

- **Copy** — click the Copy button to grab the current vector string (uses the clipboard, with a manual select-and-`Ctrl+C` fallback if clipboard access is blocked).
- **Edit or paste** — the vector field is a live text box. Paste in a vector someone else sent you, or hand-edit the codes, and the buttons and score update as you type. Unrecognized codes show a small warning underneath instead of breaking anything.

## Customizing

All the content lives in one place: the `metrics` array near the top of the `<script>` block in `fpr-calculator.html`. Each entry defines a metric's key, title, question, weight, and its levels (label, short description, and numeric value 0–10). Edit labels, add levels, or change weights there — the scoring, breakdown table, and vector string all update automatically since they're derived from the same data.

## Notes

This is a joke tool, not affiliated with NIST, FIRST, or the U.S. government. It won't help you file a CVE, but it might help you decide between the usual order and something new.
