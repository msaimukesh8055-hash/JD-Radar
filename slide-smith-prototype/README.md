# Slide Smith — prototype (parked here temporarily)

This folder is **not part of JD Radar**. It's a temporary holding area for the
`slide-smith` prototype, parked in this (public, reachable) repo because the
Claude session that built it couldn't push directly to its real home:
`https://github.com/msaimukesh8055-hash/Slide-smith`.

## What Slide Smith is

A tool that takes a small catalog of reusable presentation-template slides
(extracted from a large master deck), lets you pick one by category (KPI
tiles, ranked bars, column chart, roadmap, etc.), and auto-populates it with
your data while preserving the original slide's formatting — producing a
single-slide `.pptx` you can drop into a real deck.

## Contents

- `catalog/slide-index.json` — the library index: 20 sample slide entries,
  each with category, description, capacity, and a `fillable` map of
  role → shape_id/lines/table/chart describing what can be edited and how.
- `scripts/extract_sample.py` — extracts a chosen subset of slides (by
  1-based index) from a master `.pptx` deck into a small sample deck,
  preserving shared layouts/masters/images/charts.
- `scripts/pptx_utils.py` — shared helpers: shape lookup (incl. nested
  groups), format-preserving paragraph/run text writes, table cell writes,
  native chart data replacement.
- `scripts/populate.py` — CLI: `python3 populate.py <sample_index> <data.json>
  <output.pptx>` — loads the catalog entry, isolates that one slide from the
  sample deck, applies the data, and saves a standalone single-slide deck.
- `outputs/demo-data/*.json` — example data files (sourced from a JD Radar
  run) used to demo `bars`, `highlight-numbers`, `progressive`, and
  `column-chart` slide categories.

## Not included here (on purpose)

- `templates/sample-20.pptx` — a 20-slide extract from the source master
  deck (a proprietary corporate template). Kept out of this **public** repo
  for confidentiality. It was generated once via `extract_sample.py` from a
  user-supplied master deck and lives only in the original session's
  container.
- The 4 demo output `.pptx` files (`demo-bars.pptx`,
  `demo-highlight-numbers.pptx`, `demo-progressive.pptx`,
  `demo-column-chart.pptx`) — derived from the template above, same reason.
  These were already sent directly to the user for review.

## How to move this to the real Slide Smith repo

From a new session connected to `msaimukesh8055-hash/Slide-smith`:

1. Clone this public repo (read-only, no auth needed) and copy this folder's
   contents into the Slide-smith repo root (drop the `slide-smith-prototype/`
   prefix, drop this README or fold its notes into a proper top-level one).
2. Re-extract `templates/sample-20.pptx` by re-running
   `scripts/extract_sample.py` against the original master deck (the user
   will need to re-supply it), or have the user re-upload the 4 demo `.pptx`
   files if a working sample deck isn't available.
3. Commit and push to `Slide-smith`.
4. Once confirmed, delete this `slide-smith-prototype/` folder from the
   JD Radar repo (it was only a relay).
