# dlap-tse data/ — Phase 1 outputs (2026-08-03)

All files UTF-8. Tickers are Persian. Missing = empty string (CSV) / -99.99 (npz).

## Files

| File | Contents |
|---|---|
| `characteristics_panel.csv` | 74,147 stock-month rows (2001-03..2026-07), 357 non-financial tickers: ticker, year, month, ret_monthly + **20 characteristics** (raw). **ret_monthly winsorized 1%/99% per month** (TSE capital-increase artifacts: pre-winsorization max +1692%/month, 2.41% clipped) |
| `characteristics_z.csv` | Same, per-month cross-sectional z-scores (winsorized 1%/99% per month first) |
| `macro_panel.csv` | 312 months (2001-01..2026-12): cbirate, cpi, usd_official, brent, gold_coin, usd_market |
| `Char_all.npz` | (303, 357, 21) float32 — official CPZ layout: [:, :, 0]=return, [:, :, 1:]=20 z-chars; date/variable/ticker arrays |
| `Macro_all.npz` | (303, 6) raw macro levels, aligned to char dates; normalized at train time (train mean/std) |
| `meta.json` | Ticker list, char/macro names, dates, conventions |
| `factors_q.csv` | HXZ q-factor model: Mkt_RF, ME, IA, ROE — 214 months (2008-07..2026-06) |
| `macro_raw/` | Raw daily/annual intermediates (brent, gold, usd, WB annual) for reproducibility |

## The 20 characteristics

size, st_rev, turnover, vol (monthly) · bm, mom, roe, ag, ac, noa, nsi, gp, cei, ita,
ig, dist, oscore (annual, formation-year aligned) · investment, cbop (annual, cbop_panel) ·
dy (dividend yield, announcement-aligned).

## Conventions (critical)

- **formation_year alignment** (no look-ahead): month (y, m) with m>=7 uses formation year y;
  m<7 uses y-1. I.e. chars known July of fy, held July(fy)..June(fy+1).
- Financial firms excluded (sector: بانک/بیمه/موسسات اعتباری/نهادهای مالی) — 27 firms.
- Monthly z-scores are cross-sectional within each month.
- q-factor breakpoints: all-stock (no NYSE equivalent on TSE) — note in paper.
- Macro: annual series (cpi, usd_official) held constant within calendar year; daily series
  (brent monthly mean; gold/usd month-end last).

## Known gaps (documented in PHASE0_FINDINGS.md)

- brent (Yahoo BZ=F fallback, FRED csv endpoint was IP-blocked): 2007-08..2026-07, 2020-03 missing
- gold_coin: 2010-04..2026-08 (tgju history starts 2010)
- usd_market: 2011-11..2026-08
- usd_official: ends 2023-12 (World Bank lag)
- cpi: ends 2025-12 (World Bank lag)
- M2 and industrial production: no free monthly source — not in panel (see Phase-0 Q2)

## Rebuild

```bash
python3 scripts/build_characteristics.py   # ~1 min
python3 scripts/build_macro_panel.py       # fetches WB + tgju (+FRED/Yahoo fallback)
python3 scripts/build_qfactors.py
python3 scripts/build_npz.py
```
