To cite this please;
Eriskin, E. (2026). A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country. ISPRS International Journal of Geo-Information, 15(4), 178. https://doi.org/10.3390/ijgi15040178

# A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country

This repository contains the code, processed data, and replication materials for the manuscript:

**A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country**

## Overview

This study evaluates whether routinely available demographic composition can serve as a scalable structural baseline for provincial traffic accident rates. The modeling pipeline combines:

- compositional preprocessing using **isometric log-ratio (ILR)** transformations,
- a **Tabular Residual Network (ResNet)** for nonlinear prediction,
- pooled statistical reference models,
- tree-based tabular benchmarks,
- a simple temporal heuristic baseline,
- and spatial residual diagnostics.

The empirical application uses province–year data from **Türkiye**.

## Repository Contents

- `Accident_Model_with_Demographic_Data.ipynb` — main analysis notebook
- `masterData-rate2.xlsx` — processed province–year dataset used in the study
- `requirements.txt` — Python package requirements
- `LICENSE` — MIT License
- `README.md` — repository description and replication guide

## Data

The analysis is based on province–year data for **81 provinces in Türkiye**.

### Time coverage
- **Training period:** 2008–2019
- **Excluded period:** 2020–2021
- **Calibration/evaluation period:** 2022–2024

### Variables
The target variable is:

- **Accident Rate Per Million Population**

The explanatory variables include:

- sex composition,
- education composition,
- age composition,
- GDP per person.

### Data source
The raw data originate from the **Turkish Statistical Institute (TUIK)**.

The file `masterData-rate2.xlsx` contains the processed province–year dataset used in the manuscript.

## Preprocessing Workflow

The main preprocessing steps are:

1. Construct the province–year panel.
2. Convert sex, education, and age variables into population shares.
3. Add a small positive perturbation to compositional entries to avoid zero-related log-ratio issues.
4. Apply **ILR transformation** separately to:
   - sex composition,
   - education composition,
   - age composition.
5. Transform GDP using `log(1 + GDP)`.
6. Exclude years **2020–2021** to avoid regime disruption associated with the COVID-19 period.
7. Scale model inputs using a `RobustScaler` fitted on the **training period only**.
8. Transform the target variable using a **Yeo–Johnson PowerTransformer** for model training and invert back to the original scale for reporting.

## Models Included

The repository reproduces the following evaluated specifications:

### Proposed model
- **Tabular ResNet seed-ensemble**

### Representative single model
- **Best Seed 63**

### Pooled statistical reference models
- **Linear (pooled)**
- **Poisson (pooled)**
- **Negative Binomial (pooled)**

### Tree-based tabular benchmarks
- **Random Forest**
- **XGBoost**

### Temporal heuristic
- **LVDF (2019 Carry-Forward)**

## Training and Evaluation Design

The study uses a **time-forward design**:

- Models are trained on **2008–2019**
- Predictions are evaluated on **2022–2024**

The 2022–2024 window is used as a **calibration/evaluation period**, not as a fully independent held-out test set.

### ResNet training procedure
- Loss: **Huber loss**
- Optimizer: **AdamW**
- Learning rate: **1e-3**
- Weight decay: **0.015**
- Batch size: **32**
- Maximum epochs: **3000**
- Learning-rate adaptation: **ReduceLROnPlateau**
- Checkpointing frequency: every **10 epochs**

### Seed-ensemble construction
- Candidate seeds evaluated: **15**
- Selection rule: retain seeds with calibration/evaluation **R² > 0.50**
- Retained seeds: **5**
- Final ensemble prediction: arithmetic mean of retained-seed predictions

### Important note
The benchmarking framework is **not a fully symmetric hyperparameter-optimization exercise**.  
The ResNet uses calibration-driven checkpoint selection and seed filtering, whereas the added benchmark models are implemented as pragmatic reference specifications under fixed settings.

## Outputs Reproduced

The repository reproduces the core outputs reported in the manuscript, including:

- benchmark comparison tables,
- signed prediction error summaries,
- ECDF of signed errors,
- feature-sensitivity plot,
- year-wise error distributions,
- residuals-versus-predicted plot,
- province-level spatial residual map,
- Moran’s I statistics for observed accident rates and residuals.

## Spatial Diagnostics

Spatial validation is conducted using province-level contiguity weights based on **queen adjacency**.

The repository computes:

- **Moran’s I** for observed accident rates,
- **Moran’s I** for model residuals.

The residual map reflects **province-level mean residuals aggregated over 2022–2024**.

## How to Run

1. Install dependencies:

```bash
pip install -r requirements.txt
