# HBAAC 2026 — B2B Automotive Parts Demand Forecasting


**Team CMD · HBAAC 2026**

A practical, scalable forecasting pipeline for sparse and intermittent demand across **15,972 automotive-part SKUs**.

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)](https://jupyter.org/)
[![Forecasting](https://img.shields.io/badge/Problem-Time--Series%20Forecasting-6f42c1)](#project-overview)
[![Competition](https://img.shields.io/badge/Competition-HBAAC%202026-ff6b35)](#project-overview)

</div>

---

## Table of Contents

- [Project Overview](#project-overview)
- [Business Problem](#business-problem)
- [Repository Structure](#repository-structure)
- [Methodology](#methodology)
- [How to Run](#how-to-run)
- [Outputs](#outputs)
- [Results](#results)
- [Team](#team)
- [Limitations and Future Work](#limitations-and-future-work)
- [License](#license)

---

## Project Overview

This repository contains our solution for **HBAAC 2026**, a forecasting challenge focused on estimating future demand for a large B2B automotive-parts distributor in Vietnam.

The project is designed around a simple principle: for highly sparse and intermittent demand, **data quality, segmentation, and business-aware rules can be more valuable than an unnecessarily complex black-box model**.

### Core Objectives

- Clean and standardize transaction-level sales data.
- Handle returned parts represented by negative quantities.
- Build a continuous time series for thousands of SKUs.
- Separate active products from sparse or inactive products.
- Generate conservative forecasts that reduce costly over-forecasting.
- Produce a submission file that follows the competition format.

---

## Business Problem

The dataset presents several real-world forecasting challenges:

1. **Sparse and intermittent demand**  
   Many SKUs sell infrequently, so standard averages can be unstable.

2. **Negative transactions**  
   Returned parts create negative quantities and can distort demand signals.

3. **Large data volume**  
   Padding 15,972 SKUs across more than 1,700 days creates a very large time-series grid.

4. **Asymmetric evaluation**  
   The WRMSSE metric strongly emphasizes commercially important items and penalizes poor forecasts on high-impact series.

5. **Operational risk**  
   Over-forecasting slow-moving parts can create unnecessary inventory and working-capital costs.

---

## Repository Structure

```text
HBAAC/
├── dataset/
│   └── dataset link                         # Link to the competition dataset
│
├── notebooks/
│   ├── 01_data_cleaning_and_eda.ipynb       # Cleaning, returns handling, padding, and EDA
│   └── 02_model.ipynb                       # Feature engineering, forecasting, and export
│
├── submissions/
│   ├── .gitkeep
│   └── submission.csv                       # Generated competition submission
│
├── CV.md                                    # Project author's compact English CV
├── README.md                                # Project documentation
└── .gitignore
```

---

## Methodology

### 1. Data Cleaning and Exploratory Analysis

`notebooks/01_data_cleaning_and_eda.ipynb` prepares the raw transactions for forecasting by:

- Enforcing safe data types and validating required columns.
- Handling missing and invalid records.
- Investigating sales distributions, product activity, and profitability.
- Detecting negative quantities caused by returned parts.
- Applying rolling compensation logic to reduce return-related noise.
- Padding the SKU-date grid so that each series has a consistent time index.
- Measuring sparsity and identifying high-value product segments.

---

### 2. Sparse-Demand Segmentation

Products are divided into practical activity groups using recent sales behavior and a configurable sparsity threshold:

- **Active Segment**  
  Products with sufficient recent activity for moving-average forecasting.

- **Sparse Segment**  
  Products with irregular demand that require a conservative baseline.

- **Stale Segment**  
  Products whose last sale occurred sufficiently far in the past and should not receive an aggressive forecast.

This segmentation helps the model avoid treating every product as if it had the same demand pattern.

---

### 3. Rule-Based Forecasting

`notebooks/02_model.ipynb` applies a transparent forecasting workflow based on:

- Rolling and moving-average demand signals.
- Recent-activity controls.
- Stale-product rules.
- Calendar and business-day multipliers.
- Focused parameter search around promising configurations.
- Post-processing that clips negative forecasts to a valid inventory floor.

This design keeps the model interpretable and makes it easier to align predictions with inventory operations.

---

### 4. Negative Return Handling

Automotive-parts transactions may include negative quantities when garages return parts because of incorrect diagnosis, compatibility issues, or operational errors.

The pipeline addresses this problem by:

- Detecting negative transaction quantities.
- Separating sales behavior from return-related noise.
- Applying rolling compensation logic where appropriate.
- Preventing abnormal return transactions from dominating demand estimates.
- Preserving a consistent time-series structure for each SKU.

---

### 5. Time-Series Padding

Because many products do not appear in the transaction table every day, the data must be transformed into a continuous SKU-date grid.

The padding process enables the pipeline to:

- Represent zero-demand days explicitly.
- Calculate rolling averages correctly.
- Measure recency and inactivity.
- Identify stale SKUs.
- Apply consistent forecasting rules across all products.

---

### 6. Leakage Prevention

All features are generated using information available at the forecasting cutoff.

Future transactions are not used to:

- Calculate historical features.
- Tune forecasting rules.
- Estimate moving averages.
- Determine product activity segments.
- Generate the final forecast.

This ensures that the validation and submission process better reflects a real forecasting environment.

---

## How to Run

### Requirements

- Python 3.10 or newer
- Jupyter Notebook or JupyterLab
- Competition data downloaded locally

---

### Installation

Create and activate a virtual environment:

```bash
python -m venv .venv
```

On macOS or Linux:

```bash
source .venv/bin/activate
```

On Windows:

```bash
.venv\Scripts\activate
```

Install the required libraries:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

---

### Prepare the Dataset

1. Open [`dataset/dataset link`](dataset/dataset%20link).
2. Download the files provided by the competition organizers.
3. Place the raw data in the location expected by the notebooks.
4. Update the input path in the first notebook if you are running the project locally.

---

### Execute the Pipeline

Start Jupyter Notebook:

```bash
jupyter notebook
```

Run the notebooks in the following order:

1. Open and run:

   ```text
   notebooks/01_data_cleaning_and_eda.ipynb
   ```

2. Review the generated cleaned data and exploratory plots.

3. Open and run:

   ```text
   notebooks/02_model.ipynb
   ```

4. Confirm that the final file is written to:

   ```text
   submissions/submission.csv
   ```

> **Tip:** Use **Kernel → Restart & Run All** to reproduce the complete pipeline from a clean state.

---

## Outputs

The main deliverable is:

```text
submissions/submission.csv
```

Before submitting, verify that:

- The file contains the required identifiers and forecast columns.
- SKU and date keys are aligned with the competition sample submission.
- Forecast values are numeric and non-negative.
- There are no duplicate keys.
- There are no missing forecast rows.
- The submission file follows the required competition format.

---

## Results

The repository is structured to support the final competition submission.

Score placeholders can be updated once official results are available:

| Evaluation Stage | WRMSSE |
|---|---:|
| Internal Validation | _To be updated_ |
| Public Leaderboard | _To be updated_ |
| Private Leaderboard | _To be updated_ |

---

## Team

| Member | Responsibility |
|---|---|
| **Phan Vũ Đức Trung** | Data cleaning, negative-value handling, time padding, exploratory data analysis, and profit-weight analysis |
| **Đặng Biên Phúc Lâm** | Rule-based model development, WRMSSE tuning, and post-processing |
| **Huỳnh Phúc Nguyên** | Data cleaning, returns handling, exploratory data analysis, profit-weight analysis, and model development |
| **Đỗ Hoàng Quân** | Project management, strategic planning, and business insight synthesis |

---

## Limitations and Future Work

Potential improvements include:

- Intermittent-demand models such as Croston, SBA, and TSB.
- Hierarchical reconciliation across product, category, and distributor levels.
- Gradient-boosting models for active and high-volume SKUs.
- Deep-learning models for sufficiently dense time series.
- Probabilistic forecasts and prediction intervals.
- Automated backtesting and time-series cross-validation.
- Inventory-aware optimization.
- Joint optimization of service level, holding cost, and stockout risk.
- More advanced product lifecycle modeling.
- Demand forecasting at multiple business aggregation levels.

---

## Reproducibility Checklist

Before sharing or submitting the project, verify the following:

- [ ] The raw dataset is available locally.
- [ ] Notebook paths are configured correctly.
- [ ] The data-cleaning notebook runs successfully.
- [ ] The model notebook runs without errors.
- [ ] No future information is used in feature engineering.
- [ ] The generated submission contains the expected rows.
- [ ] Forecast values are numeric and non-negative.
- [ ] There are no duplicated SKU-date combinations.
- [ ] The final submission file is saved in `submissions/submission.csv`.

---

## License

This repository was created for **HBAAC 2026**.

Dataset usage, redistribution, and publication are subject to the terms and conditions established by the competition organizers.

---

<div align="center">

Made with data, forecasting, and business reasoning by **Team CMD**.

</div>
