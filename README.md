# Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas

This repository contains a minimal executable workflow for the paper

**Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas**

by Shintaro Mori.

The purpose of this repository is to provide a compact paper-reproduction workflow for the final multisector specification used in the paper:

```text
R = 2 fixed-eigenmode AR(1)--Binomial state-space model
y_{t,s} = mu_s + lambda_{s1} F_{1,t} + lambda_{s2} F_{2,t} + eps_{t,s}
eps_{t,s} ~ Normal(0, sigma_eps_common^2)
```

Here `F1` is the market-wide default-risk factor and `F2` is the sector-rotation factor. The observation layer is Binomial.

This repository keeps only the final common-eps workflow. Exploratory notebooks, debugging cells, failed variants, private paths, proprietary-data outputs, large cached traces, and old residual-scale variants are not included.

## Repository structure

```text
temporal-coarse-graining-multisector-default-copulas/
  README.md
  LICENSE
  requirements.txt

  data/
    SAMPLE.csv

  00.ipynb
  01.ipynb
  02.ipynb
  03.ipynb
  04.ipynb
  05.ipynb

  pdata/
    .gitkeep

  figures/
    .gitkeep

  tables/
    .gitkeep
```

## Data availability

The empirical default data analyzed in the paper are not included in this repository. Proprietary S&P default data and outputs derived from those data cannot be redistributed.

The file `data/SAMPLE.csv` is independently generated low-fidelity synthetic monthly multi-sector default-count data with the schema

```text
date,sector,obligors,defaulted
```

`SAMPLE.csv` is provided only so that the notebooks execute end to end and demonstrate the public workflow. It is not derived from, fitted to, or calibrated to the proprietary empirical data used in the paper, and it does not reproduce the paper's empirical numbers.

Researchers with access to comparable monthly default-count data can replace `data/SAMPLE.csv` with their own file using the same schema. The notebooks compute `default_rate` internally as `defaulted / obligors`.

## Notebooks

The notebooks should be run in the following order.

### `00.ipynb`

Generate low-fidelity synthetic monthly multi-sector default-count data and save it as `data/SAMPLE.csv`.

### `01.ipynb`

Data analysis and empirical coarse-graining diagnostics:

* load data;
* construct the monthly sector panel;
* compute k-month aggregation and monthly-equivalent default rates;
* summarize variance scaling, correlation/eigenvalue scaling, sector totals, and a quarterly heatmap.

Main outputs:

```text
tables/table1_sector_summary.csv
figures/figure1_monthly_default_rates.png
figures/figure2_coarse_graining_scaling.png
figures/figure3_quarterly_heatmap.png
pdata/coarse_graining_summary.csv
pdata/coarse_grained_default_counts.csv
```

### `02.ipynb`

Empirical eigenmodes:

* monthly sectoral correlation matrix;
* leading eigenvectors;
* PC score series;
* PC score ACFs.

Main outputs:

```text
tables/table3_empirical_eigenmodes.csv
pdata/monthly_sector_correlation.csv
pdata/eigenmodes.csv
pdata/pc_scores.csv
pdata/pc_score_acf.csv
```

### `03.ipynb`

Factor selection using only common-eps fixed-eigenmode AR(1)--Binomial models. The notebook compares `R = 1, 2, 3, 4` and selects `R = 2` as the parsimonious and stable main specification.

Main outputs:

```text
tables/table2_common_eps_factor_selection.csv
figures/figure4_factor_selection.png
pdata/factor_selection.csv
pdata/selected_factor_dimension.json
```

### `04.ipynb`

Final `R = 2` common-eps analysis:

* fit or load the final `R = 2` common-eps model;
* compute posterior-style diagnostics;
* generate posterior predictive paths;
* perform temporal coarse-graining diagnostics;
* construct annual correlation matrices;
* construct posterior-implied rank copulas;
* summarize sector-wise variance decomposition.

Main outputs:

```text
tables/table4_r2_common_eps_parameters.csv
tables/table5_r2_posterior_predictive_copula_variance.csv
figures/figure5_r2_factor_diagnostics.png
figures/figure6_r2_posterior_predictive_coarse_graining.png
figures/figure7_r2_rank_copula_variance_decomposition.png
figures/figure10_annual_correlation_matrices.png
pdata/r2_common_eps_model.json
pdata/r2_posterior_predictive_summary.csv
pdata/copula_summary.csv
```

### `05.ipynb`

Annual `k = 12` forecast comparison for:

```text
B0 = Binomial-best static baseline
B1 = Beta-Binomial-best static baseline
B2 = One-factor dynamic common-eps model
B3 = Two-factor dynamic common-eps model
```

At each forecast origin, fixed eigenmode loadings are recomputed using only the training window. Full-sample eigenvectors are not used in rolling forecasts.

Main outputs:

```text
tables/table6_annual_forecast_scores.csv
tables/table7_forecast_comparison.csv
figures/figure8_forecast_scores.png
figures/figure9_forecast_calibration.png
figures/figure11_cumulative_forecast_log_score.png
pdata/annual_forecast_scores.csv
```

## Input data format

The notebooks expect `data/SAMPLE.csv` to have the following columns:

```text
date,sector,obligors,defaulted
```

where:

* `date` is the monthly date;
* `sector` is the sector name;
* `obligors` is the number of obligors in that sector-month;
* `defaulted` is the number of defaults in that sector-month.

## Installation

The code was developed in Python. A typical environment can be prepared with:

```bash
pip install -r requirements.txt
```

The required packages are:

* `numpy`
* `pandas`
* `scipy`
* `matplotlib`
* `pymc`
* `arviz`
* `jupyter`

## Running the analysis

Start Jupyter:

```bash
jupyter notebook
```

Then run:

```text
00.ipynb
01.ipynb
02.ipynb
03.ipynb
04.ipynb
05.ipynb
```

The notebooks write processed files to `pdata/`, figures to `figures/`, and tables to `tables/`.

## Reproducibility note

The synthetic dataset is deterministic given the seed in `00.ipynb`. Simulation-based posterior predictive and forecast summaries may vary slightly across platforms, package versions, and random seeds.

The sample data are intended for workflow testing only. The numerical values obtained from `SAMPLE.csv` should not be compared with the empirical results in the paper.

## Citation

If you use this code, please cite the paper:

```text
Shintaro Mori,
Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas,
2026.
```

## License

This repository is released under the MIT License. See `LICENSE` for details.
