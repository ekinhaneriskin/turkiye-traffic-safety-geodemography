# A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country

This repository contains the code, processed data, and replication materials for the manuscript:

**"A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country"**

---

## Overview

This study evaluates whether routinely available demographic composition can serve as a scalable structural baseline for provincial traffic accident rates. The modeling pipeline combines:

* **Compositional Preprocessing:** Using Isometric Log-Ratio (ILR) transformations.
* **Deep Learning:** A Tabular Residual Network (ResNet) for nonlinear prediction.
* **Statistical Benchmarks:** Pooled Linear, Poisson, and Negative Binomial models.
* **Machine Learning Benchmarks:** Random Forest and XGBoost.
* **Spatial Analysis:** Residual diagnostics and Moran’s I statistics.

The empirical application uses province–year data from **Türkiye**.

---

## Repository Contents

* `Accident_Model_with_Demographic_Data.ipynb` — Main analysis notebook.
* `masterData-rate2.xlsx` — Processed province–year dataset used in the study.
* `requirements.txt` — Python package requirements.
* `LICENSE` — MIT License.
* `README.md` — Repository description and replication guide.

---

## Data

The analysis is based on province–year data for **81 provinces in Türkiye**.

### Time Coverage
* **Training Period:** 2008–2019
* **Excluded Period:** 2020–2021 (COVID-19 regime disruption)
* **Calibration/Evaluation Period:** 2022–2024

### Variables
* **Target:** Accident Rate Per Million Population.
* **Explanatory:** Sex composition, education composition, age composition, and GDP per capita.
* **Source:** Raw data originate from the Turkish Statistical Institute (TUIK).

---

## Preprocessing Workflow

The main preprocessing steps implemented in the notebook:
1. Construct the province–year panel.
2. Convert sex, education, and age variables into population shares.
3. Apply **ILR transformation** to compositions to address the Aitchison geometry.
4. Log-transform GDP: $\log(1 + \text{GDP})$.
5. Scale inputs using a `RobustScaler` (fitted on training data only).
6. Target transformation via **Yeo–Johnson PowerTransformer**.

---

## Models Included

### 1. Proposed Model
* **Tabular ResNet Seed-Ensemble:** Arithmetic mean of predictions from the top 5 performing seeds (retained if $R^2 > 0.50$ in the calibration window).

### 2. Benchmarks
* **Representative Single Model:** Best performing single seed (Seed 63).
* **Statistical:** Linear, Poisson, and Negative Binomial (pooled).
* **Tree-Based:** Random Forest and XGBoost.
* **Heuristic:** LVDF (2019 Last Value Carried Forward).

---

## Training and Evaluation Design

* **Design:** Time-forward split (Train: 2008–2019; Eval: 2022–2024).
* **Loss Function:** Huber Loss.
* **Optimizer:** AdamW (LR: 1e-3, Weight Decay: 0.015).
* **Spatial Validation:** Province-level contiguity weights based on queen adjacency.

---

## How to Run

1.  **Clone the repository:**
    ```bash
    git clone [https://github.com/ekinhaneriskin/turkiye-traffic-safety-geodemography.git](https://github.com/ekinhaneriskin/turkiye-traffic-safety-geodemography.git)
    cd turkiye-traffic-safety-geodemography
    ```

2.  **Install dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

3.  **Run the analysis:**
    Launch Jupyter Notebook or JupyterLab and open `Accident_Model_with_Demographic_Data.ipynb`.

---

## Citation

If you find this research or code useful, please cite our paper:

> Eriskin, E. (2026). A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country. *ISPRS International Journal of Geo-Information*, 15(4), 178. https://doi.org/10.3390/ijgi15040178

```bibtex
@article{eriskin2026traffic,
  title={A Scalable Geodemographic Baseline for Traffic Safety Monitoring in a Middle-Income Country},
  author={Eriskin, Ekinhan},
  journal={ISPRS International Journal of Geo-Information},
  volume={15},
  number={4},
  pages={178},
  year={2026},
  publisher={MDPI},
  doi={10.3390/ijgi15040178}
}
