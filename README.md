# Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas

This repository is for the paper

**Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas**

by Shintaro Mori.

## Repository Status

This repository is currently under construction.

The public reproduction workflow will be rebuilt from cleaned excerpts of the actual common-eps research notebooks used for the paper. The temporary demonstration notebooks and generated outputs that were previously present have been removed.

The intended public workflow will keep the simple numbered-notebook style used in the related repository:

```text
00.ipynb
01.ipynb
02.ipynb
03.ipynb
04.ipynb
05.ipynb
```

Replacement notebooks have not yet been added.

## Model Scope

The public workflow will focus on the final common-eps multisector specification:

```text
R = 2 fixed-eigenmode AR(1)--Binomial state-space model
y_{t,s} = mu_s + lambda_{s1} F_{1,t} + lambda_{s2} F_{2,t} + eps_{t,s}
eps_{t,s} ~ Normal(0, sigma_eps_common^2)
```

The old sector-specific residual model is not part of the intended public workflow.

## Data Availability

The empirical default data analyzed in the paper are not included in this repository. Proprietary S&P default data and outputs derived from those data cannot be redistributed.

The file `data/SAMPLE.csv`, when present, is independently generated low-fidelity synthetic monthly multi-sector default-count data. It is provided only to document the expected public input schema and to support future workflow testing.

`data/SAMPLE.csv` is not derived from, fitted to, or calibrated to the proprietary empirical data used in the paper, and it does not reproduce the paper's empirical numbers.

The expected input schema is:

```text
date,sector,obligors,defaulted
```

where:

* `date` is the monthly date;
* `sector` is the sector name;
* `obligors` is the number of obligors in that sector-month;
* `defaulted` is the number of defaults in that sector-month.

## Current Repository Structure

```text
temporal-coarse-graining-multisector-default-copulas/
  README.md
  LICENSE
  requirements.txt

  data/
    SAMPLE.csv

  pdata/
    .gitkeep

  figures/
    .gitkeep

  tables/
    .gitkeep
```

## Installation

The eventual workflow will be developed in Python. A typical environment can be prepared with:

```bash
pip install -r requirements.txt
```

## Reproducibility Note

This repository does not currently contain the public reproduction notebooks. The workflow will be added after the paper code is cleaned to remove private paths, exploratory variants, proprietary-data outputs, and large cached traces.

## Citation

If you use this code, please cite the paper:

```text
Shintaro Mori,
Temporal Coarse-Graining of Multi-Sector Default Count Data Generates Posterior-Implied Copulas,
2026.
```

## License

This repository is released under the MIT License. See `LICENSE` for details.
