# Standing conventions for the automated prediction routine

This file is read automatically by Claude Code sessions working in this repo,
including the scheduled routine that publishes race predictions to the
`claude/predictions` branch. Follow these conventions in addition to whatever
the routine's own prompt specifies.

## Always include a Liam Lawson finish prediction

Every published race prediction (each `<article class="race">` added to
`index.html` on `claude/predictions`) must include a dedicated Liam Lawson
finish-prediction section, regardless of his grid position or team that
weekend. Place it directly after "Last Classified Finisher" and before
"Notable Driver Call", matching the format already used for the 2026 Belgian
Grand Prix entry:

```html
<h3 class="section">Liam Lawson's Finish</h3>
<div class="callout">
  <span class="label">Best call: P&lt;n&gt; · Range: P&lt;low&gt;–P&lt;high&gt;</span>
  <span class="tag fact">FACT</span>...grid position, team, qualifying context...
  <span class="tag inference">INFERENCE</span>...reasoning for the predicted range...
</div>
```

Research his actual grid slot and current team for that race weekend (he has
moved between Racing Bulls and Red Bull during the 2026 season) the same way
the rest of the prediction is researched — live web search, not memory.
