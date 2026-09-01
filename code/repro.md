# Exact reproduction sequence

Every command below runs from the package root with:

```bash
export DLAP_ROOT=$(pwd)
# Iran:  unset DLAP_COUNTRY
# T\u00fcrkiye: export DLAP_COUNTRY=TR
# Pakistan: export DLAP_COUNTRY=PK
```

`results*/` in this package **are** the outputs of these commands (the exact
artifacts underlying the manuscript). Re-running them regenerates the same
numbers deterministically (seeded torch/numpy; LASSO CV seed fixed).

## 1. Benchmark models (Table `tab:bench`)

```bash
for C in "" TR PK; do
  [ -n "$C" ] && export DLAP_COUNTRY=$C || unset DLAP_COUNTRY
  python code/run_e1.py                 # Market, FF5, q-factor, PCA(5), LASSO
  python code/linear_sdf_benchmark.py --charset sy   # Linear SDF (SY)
  python code/linear_sdf_benchmark.py --charset all  # Linear SDF (all)
done
```

Outputs: `results{,_tr,_pk}/e1_*.{csv,_pooled_series.csv}`, `linear_sdf_*`.

## 2. Deep SDF battery (Table `tab:deep`)

Seeds 42/43/44, specifications E2/E3/E4A/E4B/E5A/E5B/E8/E8B:

```bash
# one specification example (E2 baseline, seed 42):
python code/train_e2.py --charset sy --states lstm --seed 42
# full grid: see code/rerun_audit.sh (loops specs x seeds x markets)
bash code/rerun_audit.sh
```

Outputs: `results{,_tr,_pk}/{e2,e3,e4a,...}*_results.csv` + pooled series +
alpha cells.

## 3. Sharpe-difference moving-block bootstrap (Table `tab:boot`)

```bash
python code/sharp_diff_bootstrap.py        # block 6, 10,000 reps, seed 42
python code/bench_leverage_check.py        # unit-gross-leverage normalization
```

## 4. Paired window-level RMS-alpha bootstrap (Table 4)

```bash
python code/rms_window_bootstrap.py        # consumes *_alpha_cells.csv
```

## 5. Sign convention / loss-symmetry / Method B (Tables `tab:sign`, `tab:methodb`)

```bash
python code/train_e2.py --charset sy --states lstm --seed 42 --dump-mechanism
python code/method_b_summary.py            # ex-ante sign pin, lambda 1 and 10
```

## 6. SPA / Reality Check (Table `tab:spa`)

```bash
python code/spa_test.py                    # Hansen (2005) + White (2000), blocks 6/12
```

## 7. Loadings + block bootstrap (Table `tab:loadings`)

```bash
python code/e6_loadings.py
python code/loadings_bootstrap.py
```

## 8. Robustness

```bash
python code/train_e2_lag.py                # lag-alignment check
python code/seed_sensitivity.py            # 13-seed expansion
python code/train_e2_ens.py                # seed-ensemble check
python code/common_calendar.py             # common-calendar check
python code/placebo_summary.py             # drop-rule placebos
python code/e7_subperiod.py                # boom-bust subperiods
python code/e8_mechanism.py                # tail microstructure diagnostics
python code/ae_benchmark.py                # conditional-autoencoder benchmark
python code/ipca_proper.py                 # IPCA benchmark
```

## 9. Canonical tables + manuscript verification

```bash
python code/build_master_results.py
python code/build_canonical_3c.py
python code/verify_manuscript_3c.py        # cross-checks every manuscript number
```

Final gate: `verify_manuscript_3c.py` must report zero errors.
