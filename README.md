# f1predict

Predicts the winners of the F1 — then grades itself against the official result and
keeps a running accuracy record.

Single static `index.html`. No build step, no backend, no network calls at runtime.

## How it works

Predictions are written as prose (the FACT / INFERENCE / SPECULATION analysis), but
every scoreable call is *also* recorded in a machine-readable ledger embedded in the
page:

```html
<script id="race-data" type="application/json"> … </script>
```

A small inline script reads that ledger at load and computes every number shown —
the per-race scorecard and the running track record. **No score is ever hand-written.**
That's the point: a record you can edit after the fact isn't a record.

## Adding a race

**Before the race** — append an object to `races[]` with `"result": null`:

```json
{
  "id": "2026-11",
  "season": 2026, "round": 11, "event": "Hungarian Grand Prix", "date": "2026-08-02",
  "predicted": ["ANT", "NOR", "LEC", "VER", "PIA"],
  "probs": { "ANT": 0.30, "NOR": 0.26, "LEC": 0.15, "VER": 0.14, "PIA": 0.09 },
  "result": null
}
```

Then add the prose section with `data-race="2026-11"` on the `<article>`, `data-code`
on each `<li class="pick">`, and an empty `<section data-result-for="2026-11">` where
the scorecard should render. Races with `"result": null` render the prediction only.

**After the race** — fill in `result` from the official classification:

```bash
curl -s -H "User-Agent: Mozilla/5.0" \
  "https://api.jolpi.ca/ergast/f1/2026/11/results/?format=json"
```

Use the **post-stewards** classification — if a penalty changed the podium, the amended
result is the truth. `classification` is `[position, driverCode]` pairs, with `"R"` as
the position for retirements.

Note: that API rejects some clients with a 403 unless a browser `User-Agent` is sent.

## Metrics

| Metric | Meaning |
|---|---|
| **Winner** | Did the P1 call land. Binary. |
| **Podium hits** | How many predicted top-3 drivers finished top 3, order ignored (0–3). |
| **Top 5 hits** | Same for the top 5, as a set (0–5). |
| **Exact positions** | Right driver in the right slot (0–5). |
| **Brier score** | Mean `(p − outcome)²` over every published probability. Lower is better. |

The Brier score is the one that matters. Winner accuracy can't tell a lucky 20% call
from a well-reasoned 60% one, and it doesn't punish being confidently wrong. Brier does.

Side calls and wildcards are displayed with hit/partial/miss verdicts but are **not**
folded into the headline scores — they're narrative calls, judged by hand.

The track record hides nothing and shows a sample-size warning under 5 graded races,
because with n=1 every number on the card is noise.
