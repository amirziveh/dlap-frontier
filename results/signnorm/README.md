# Alternative sign-normalized pooled series — provenance note

The two files in this folder (`e2_pooled_series.csv`, `e8_pooled_series.csv`) are
**experimental alternative aggregates** of the pooled rolling series, built with an
alternative sign-normalization convention that differs from the canonical release.

- The **canonical** pooled series used by the manuscript — including Figure 1 and
  every table comparison — live at `results/e2_pooled_series.csv` and
  `results/e8_pooled_series.csv` (raw, as-trained orientation, seed-wise).
- The two versions are **not interchangeable**: e.g. the pooled E2 Sharpe in this
  folder is 1.230229, versus 1.332360 for the canonical series used in the paper.
- Nothing in the published paper, its tables, or its figure is computed from the
  files in this folder. They are retained only for provenance of earlier
  exploratory work; if you are reproducing the paper, use the canonical files
  under `results/`.
