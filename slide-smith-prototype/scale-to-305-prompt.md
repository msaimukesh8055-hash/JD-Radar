# Paste-ready prompt: scale Slide Smith catalog to ~305 slides

Paste this into the Slide-smith session after uploading
`16x9_Timesaver_PPT.pptx` (full 305-slide master deck).

---

You are building "Slide Smith" — a tool that takes my data, picks the right
reusable template slide from a large master deck, auto-populates it while
preserving the slide's original formatting, and hands me back a single-slide
.pptx I can drop into a real deck.

The repo already has a working prototype validated on 20 slides:
`catalog/slide-index.json` (20 entries), `scripts/{populate,pptx_utils}.py`,
and `outputs/demo-data/*.json`. The engine works — text/table/chart
population preserves formatting. Your job now is to (1) scale the catalog
from 20 → all ~305 slides of the uploaded master deck, (2) generalize the
populate engine to work directly off the master deck, and (3) build the
project architecture and the `/build-slide` entry point.

## Ground truth: keep the existing 20 entries as-is

`catalog/slide-index.json` already has 20 hand-verified entries (correct
shape_ids, role names, categories, capacities) for master_index values:
1, 4, 10, 12, 19, 20, 21, 24, 26, 57, 62, 74, 86, 98, 123, 138, 159, 214,
221, 256. Carry these 20 forward UNCHANGED into the final catalog (don't
re-derive or re-categorize them). Use their category names and role-naming
conventions (title, subtitle, item1_heading, item1_body, tile3_number,
tile3_desc, step2_heading, chart, etc.) as the house style for the other
~285 entries, so the catalog is internally consistent.

## Part 0: generalize the populate engine (do this first, it's small)

`sample-20.pptx` / `extract_sample.py` are no longer needed — drop that
intermediate step entirely. `populate.py` should:

- Take the master deck (`templates/16x9_Timesaver_PPT.pptx`) directly.
- Given a catalog entry's `master_index`, copy the master deck and delete
  every slide except that one (reuse the existing `remove_other_slides`
  logic from `pptx_utils.py`, just driven by `master_index` instead of a
  sample-deck position).
- Everything else (apply_data, chart/table/text writers) stays the same.

Quick sanity check: re-run the 4 existing demos
(`outputs/demo-data/{bars,highlight-numbers,progressive,column-chart}.json`)
through the new path against `master_index` 21/24/20/74 and confirm they
still produce correct single-slide .pptx files.

## Part 1: scale the catalog to ~305 slides

Build a full `catalog/slide-index.json` covering every CONTENT slide. Skip
section dividers and the TOC unless genuinely reusable as a template (note
each skip and why in your final report).

CRITICAL — do this with SUBAGENTS, not one sequential pass. One agent
cataloging 305 slides will exhaust its context. Instead:

- Write `scripts/dump_shapes.py` — a reusable script that dumps, for each
  slide: master_index, every shape's id + name + type + (for text frames)
  current text and paragraph count, (for tables) rows×cols + cell text,
  (for charts) chart_type + categories/series. Save to
  `catalog/raw-shape-dump.json`.
- Split the ~285 remaining slides (everything except the 20 already done)
  into batches of ~30. Spawn one subagent PER BATCH (parallel — multiple
  Task calls in a single message). Give each subagent ONLY its slice of the
  raw dump, the schema below, and the 20 existing entries as style
  reference. Each returns a compact JSON array of entries for its batch.
  Consider running these batch-cataloging subagents on a cheaper/faster
  model (e.g. Sonnet) since they're pattern-matching against a fixed schema
  and 20 worked examples — save your own (the orchestrator's) reasoning for
  the merge, engine generalization, and architecture work.
- Merge all batches + the original 20 into one `catalog/slide-index.json`,
  sorted by master_index, with sequential `sample_index` (1..N) reassigned
  at merge time.

Schema (identical to the existing 20 — match it exactly):

```json
{
  "sample_index": "<int, 1-based, assigned at merge>",
  "master_index": "<int, 1-based position in the 305-slide deck>",
  "label": "<the slide's own heading/title text>",
  "section": "<which of the deck's 9 sections it belongs to>",
  "category": "<short kebab-case type — reuse existing category names where a slide matches one (kpi-numbers-6, ranked-bars-3item, chart-column, timeline-8point, process-steps-3, table-2col, funnel-6stage, org-chart, matrix-2x2-portfolio, etc.); invent new ones consistently otherwise>",
  "description": "<1-2 sentences: what it shows and when to use it>",
  "capacity": "{ e.g. items: 3, body_lines: 3 - the natural limits }",
  "fillable": [
    "text:  { role, shape_id, lines, current }",
    "table: { role, shape_id, table: [rows, cols] }",
    "chart: { role, shape_id, chart_type, current_categories, current_series }"
  ]
}
```

Notes: `shape_id` is unique within a slide, found via recursive search
(incl. nested groups). `lines` = paragraph count in that text frame. Group
near-duplicate slides under the same category; note low-reusability ones in
the description rather than dropping them.

Validate the merged file:
`python3 -c "import json; json.load(open('catalog/slide-index.json'))"`

## Part 2: architecture

```
Slide-smith/
├── catalog/slide-index.json           # full catalog (Part 1)
├── templates/16x9_Timesaver_PPT.pptx  # master deck (only file needed now)
├── scripts/                            # dump_shapes, populate, pptx_utils
├── outputs/                            # generated slides + demo-data/
├── .claude/
│   ├── commands/build-slide.md         # the intake/Q&A entry point
│   └── skills/slide-catalog/SKILL.md
└── CLAUDE.md
```

- `CLAUDE.md`: objective, pipeline (intake → pick slide → populate → return
  .pptx), catalog schema, how to run populate.py, and constraints (preserve
  formatting; never fabricate data; one slide per output unless asked).
- `.claude/skills/slide-catalog/SKILL.md`: how to read slide-index.json,
  match a user's data shape to a category, and choose the best slide.

## Part 3: /build-slide command

Create `.claude/commands/build-slide.md`. When I run it:

1. Look at the data/intent I provide (raw text, numbers, a table, etc.).
2. If the best slide category is ambiguous, ask me 1-3 short questions
   (chart? numbers/KPIs? roadmap? ranked bars? table? timeline? funnel?
   process? org chart? matrix?) and roughly how many items.
3. Pick the matching catalog entry, build a data.json mapping roles→values
   (respect each role's `lines`/`capacity` — truncate or split sensibly).
4. Run populate.py to produce a single-slide .pptx in outputs/.
5. Hand me the file and a one-line note on which slide it used and why.

## Verification — read this before claiming anything works

This sandbox's LibreOffice cannot convert .pptx → image/PDF (a pre-existing,
environment-wide issue, not your code). You will NOT be able to visually
preview slides. So:

- Verify every populated slide PROGRAMMATICALLY: dump the written text,
  table cells, and chart categories/series back out with python-pptx and
  confirm they match the input data.
- NEVER claim you "visually checked" or "confirmed it looks correct" — you
  can't. Say what you actually verified (e.g. "text and chart data written
  correctly per python-pptx dump; formatting not visually confirmed").
- Send me the .pptx outputs (via the file-send tool) so I can open them in
  PowerPoint and confirm formatting/layout myself.

## Deliverables

- Full `catalog/slide-index.json` (all content slides, 20 existing + ~285 new)
- Generalized `scripts/{dump_shapes,populate,pptx_utils}.py` (no sample-deck
  dependency)
- `CLAUDE.md`, `.claude/skills/slide-catalog/SKILL.md`,
  `.claude/commands/build-slide.md`
- README documenting the catalog schema and how to run things
- Commit and push everything

Report when done: total slides cataloged, slides skipped (with reasons), and
confirmation that the 4 demo slides still populate correctly through the new
master-deck-direct path.
