# Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas

This repository corresponds to the code repository described in the submitted manuscript as:

https://github.com/shintaro-mori/multisector-default-coarse-graining

The actual public repository is hosted at:

https://github.com/shintaromori/multisector-default-coarse-graining


This repository contains the analysis workflow for the paper:

**Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas**

The project studies how persistent low-rank monthly credit-state dynamics generate horizon-dependent sectoral dependence structures after temporal coarse-graining. The empirical analysis uses monthly sector-level default-count data and fits fixed-eigenmode AR(1)--Binomial state-space models with a common residual scale.

## Repository purpose

This repository is intended to provide a transparent analysis workflow for the paper. It contains notebooks for:

1. generating synthetic demonstration data,
2. constructing temporally aggregated default-count panels,
3. selecting the number of fixed eigenmode factors,
4. generating posterior diagnostics for the two-factor model, and
5. evaluating annual forecasts against static baselines.

The repository is **not** a public release of the empirical default data used in the paper.

## Data availability

The empirical sector-level default-count data analyzed in the paper are derived from proprietary historical default datasets and are therefore **not included** in this repository.

Instead, the repository includes a synthetic demonstration dataset:

```text
data/SAMPLE.csv
```

This file has the same column structure and monthly frequency expected by the notebooks, but it is independently generated synthetic data. It is provided only for code execution and workflow demonstration. It is **not intended to reproduce** the empirical numerical results, figures, or tables reported in the paper.

Researchers with access to comparable empirical data can reproduce the paper workflow by placing the real data file at:

```text
data/SP_monthly_sector_and_all.csv
```

If this file exists, the notebooks automatically use it. If it does not exist, the notebooks fall back to:

```text
data/SAMPLE.csv
```

## Expected input format

The notebooks expect a monthly sector-level CSV file with the following columns:

```text
date
year
month
quarter
half
sector
bonds
defaulted
upgrade
same
downgrade
non_rated
default_rate
```

The empirical data used in the paper contain `ALL` plus 13 sector categories. In the expected format, `ALL` is the aggregate of the sector-level rows for each month.

## Notebook workflow

Run the notebooks in the following order.

### 01. Generate synthetic data

```text
01_generate_synthetic_multisector_data.ipynb
```

Generates:

```text
data/SAMPLE.csv
```

This notebook does not use the empirical data. It creates a low-fidelity synthetic monthly sector-level default-count panel with the same column structure as the real input file.

### 02. Data coarse-graining and empirical diagnostics

```text
02_data_coarse_graining.ipynb
```

Reads either:

```text
data/SP_monthly_sector_and_all.csv
```

or, if unavailable:

```text
data/SAMPLE.csv
```

Main outputs include:

```text
pdata/SP_monthly_sector_and_all_kmonth.csv
pdata/empirical_k1_eigenvalues.csv
pdata/fixed_eigenvectors_k1_v1_to_v4.csv
pdata/empirical_pc1_pc4_scores_k1.csv

tables/table01_monthly_data_summary_by_sector.csv
tables/table03_sector_and_ALL_variance_scaling.csv
tables/table05_empirical_eigenvalue_scaling_long.csv
tables/table07_empirical_k1_eigenvalues.csv
tables/table08_empirical_k1_eigenvectors_v1_to_v4.csv
tables/table09_empirical_pc1_pc4_acf_k1.csv

figures/fig01_variance_scaling_monthly_equivalent_rates.png
figures/fig02_empirical_eigenvalue_scaling.png
figures/fig03_empirical_k1_eigenvectors_v1_v2.png
figures/fig03a_empirical_k1_eigenvalue_spectrum.png
figures/fig04_empirical_pc1_pc4_acf_k1.png
figures/figS01_quarterly_sector_default_rate_heatmap_grayscale.png
```

### 03. Factor selection with common residual scale

```text
03_factor_selection_common_eps.ipynb
```

Fits fixed-eigenmode AR(1)--Binomial models with a common residual scale for ranks `R = 1, 2, 3, 4`.

The main model is:

```text
R=2 fixed-eigenmode AR(1)--Binomial state-space model
+ common iid residual scale
+ Binomial observation layer
```

The notebook compares eigenvalue-scaling diagnostics and supports the choice of `R=2` as a parsimonious and stable baseline.

### 04. Two-factor posterior diagnostics

```text
04_diagnostics_R2_common_eps.ipynb
```

Uses the `R=2` posterior output from notebook 03 and generates diagnostics for the main two-factor common-residual model.

### 05. Forecast evaluation

```text
05_forecast_evaluation_common_eps.ipynb
```

Compares static and dynamic annual forecast models:

```text
B0: Binomial-best
B1: Beta-Binomial-best
B2: One-factor dynamic
B3: Two-factor dynamic
```

At each forecast origin, the fixed eigenmode loading directions are re-estimated using only the corresponding training window. Thus, no future observations enter the construction of the forecast loading directions.

Rolling Bayesian fit files are stored under:

```text
pdata/bayes_rolling_fits_k12_B2_B3_common_eps/
```

These files are large and are normally excluded from Git version control.

## Suggested directory structure

```text
.
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   └── SAMPLE.csv
├── notebooks/
│   ├── 01_generate_synthetic_multisector_data.ipynb
│   ├── 02_data_coarse_graining.ipynb
│   ├── 03_factor_selection_common_eps.ipynb
│   ├── 04_diagnostics_R2_common_eps.ipynb
│   └── 05_forecast_evaluation_common_eps.ipynb
├── pdata/
├── tables/
├── figures/
├── tables_paper_common_eps/
└── figures_paper_common_eps/
```

The output directories are created automatically by the notebooks when needed.

## Python environment

The notebooks were developed with Python 3.11. The main Python packages are:

```text
numpy
pandas
matplotlib
scipy
pymc
arviz
xarray
netcdf4
```

A minimal installation command is:

```bash
python -m pip install numpy pandas matplotlib scipy pymc arviz xarray netcdf4 ipykernel
```

If using VS Code or Jupyter, register the environment as a Jupyter kernel with:

```bash
python -m ipykernel install --user --name multisector-default-risk --display-name "Python (multisector-default-risk)"
```

## Reproducibility notes

* The empirical results in the paper require the proprietary empirical data file `data/SP_monthly_sector_and_all.csv`.
* The included `data/SAMPLE.csv` allows the notebooks to run, but it does not reproduce the empirical figures and tables in the paper.
* Bayesian sampling results may vary slightly across machines because of random sampling and numerical differences.
* Large posterior files, rolling fit files, and generated figures/tables should generally be regenerated from the notebooks rather than committed to Git.

## Git ignore recommendation

The following files and directories should usually be excluded from version control:

```text
.ipynb_checkpoints/
__pycache__/
*.pyc

.DS_Store

pdata/bayes_rolling_fits*/
pdata/*.nc
pdata/*.npy
pdata/*.npz
*.pkl
*.pickle
*.joblib
*.h5
*.hdf5
```

Depending on whether generated figures and tables should be tracked, you may also exclude:

```text
figures/*.png
figures/*.pdf
tables/*.csv
tables/*.tex
figures_paper_common_eps/*.png
figures_paper_common_eps/*.pdf
tables_paper_common_eps/*.csv
```

## Citation

If you use this repository, please cite the associated paper:

```text
Shintaro Mori,
Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas,
2026.
```

## License

Please see the `LICENSE` file for license information.

## Contact

For questions about the analysis workflow, please contact the author of the associated paper.

