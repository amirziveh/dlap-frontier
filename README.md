# dlap-frontier

Replication package for **"Does Deep-Learning Asset Pricing Transfer to Frontier
Markets? A Deep Stochastic Discount Factor for Iran, Türkiye, and Pakistan."**

The paper estimates the deep-learning stochastic discount factor (SDF) of
Chen, Pelger & Zhu (2024) — a *common* kernel
`M_{t+1} = 1 − (1/N_t) Σ_i ω_t(i) R^e_{t+1,i}` with network-learned weights over
characteristics and macro states — under one identical pipeline on three
frontier/near-frontier exchanges: the Tehran Stock Exchange (TSE), Borsa
Istanbul (BIST), and the Pakistan Stock Exchange (PSX).

## What is included

```
code/         all estimation, evaluation, bootstrap, and verification scripts
data/         derived estimation panels (one dir per market: data/ = TSE,
              data_tr/ = BIST, data_pk/ = PSX)
results/      every result table underlying the manuscript (results/ = TSE,
              results_tr/, results_pk/), incl. per-window alpha cells,
              pooled series, bootstraps, SPA/Reality Check outputs
figures/      manuscript figures (regenerable via code/q1_artifacts.py)
```

Each `data*/` directory contains the **derived estimation panels actually
consumed by the code**: the characteristic array (`Char_all.npz`, official CPZ
layout), macro panel (`Macro_all.npz`), monthly characteristic CSVs
(raw + cross-sectionally z-scored), winsorized FF5/q-factor portfolios,
the risk-free series, and `meta.json` (tickers, dates, conventions).

**Raw source data is not included.** Monthly prices come from TSETMC (Tehran),
the EVDS API of the Central Bank of the Republic of Türkiye and KAP filings
(Türkiye), and PSX end-of-day files plus company annual reports (Pakistan).
Redistribution of those raw feeds is not permitted by their terms of use; the
derived panels here are sufficient to re-run every estimation, evaluation and
bootstrap in the paper. Scripts that build panels from raw sources
(`*_build_*`, VLM extraction pipeline) are documented in the manuscript and
available from the authors on request.

## Environment

Python ≥ 3.10. Install pinned dependencies:

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

## Reproducing the results

All scripts are driven by two environment variables; no source edits needed:

```bash
export DLAP_ROOT=$(pwd)          # package root
# DLAP_COUNTRY: unset = Iran/TSE, TR = Türkiye/BIST, PK = Pakistan/PSX
```

`code/repro.md` lists the exact command sequence (benchmarks, deep battery,
bootstraps, SPA tests) and `code/rerun_audit.sh` runs the full battery
end-to-end. Every training run is seeded (42/43/44) and deterministic given
the packaged panels.

## Citing

If you use this package, cite the paper and the Zenodo DOI (see the repository
badge / `CITATION.cff`).

## License

Code: MIT. Derived panels: CC-BY-4.0.
