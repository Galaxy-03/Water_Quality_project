# Water_Quality_project
# Groundwater Quality Prediction — Goa & Maharashtra (WQTI)


## 📌 Overview

This project predicts the degradation of groundwater quality across districts in **Goa and Maharashtra** using a custom Water Quality Trend Index (WQTI) and deep learning (LSTM). A comparative study with Linear Regression is also included to evaluate model performance.

The project was built as part of a research internship and uses real-world data sourced from government groundwater monitoring stations.

---

## 🎯 Problem Statement

Groundwater quality is degrading across India due to agricultural runoff, industrial discharge, and urbanisation. Most monitoring is reactive — this project builds a **predictive system** that forecasts future WQTI values (2021–2030) at a district level, enabling proactive intervention.

---

## 🧪 What is WQTI?

The **Water Quality Trend Index (WQTI)** is a composite index engineered from 5 groundwater parameters:

| Parameter | Direction | Why |
|---|---|---|
| pH | Higher = Better | Neutral pH indicates safe water |
| Conductivity | Higher = Better | Indicates dissolved minerals |
| BOD (Biochemical Oxygen Demand) | Lower = Better | High BOD = contamination |
| Nitrate (N + Nitrite N) | Lower = Better | High nitrate = agricultural runoff risk |
| Total Coliform | Lower = Better | High coliform = bacterial contamination |

Each parameter is **MinMax normalized** (0–1). BOD, Nitrate, and Total Coliform are inverted (1 − value) so that higher WQTI always means better water quality. Final WQTI is a **weighted average** of all normalized values.

---

## 🏗️ Project Architecture

```
Data Loading (7084_source_data.csv)
        ↓
Filtering: Goa + Maharashtra only
        ↓
Feature Engineering:
  - Extract district from station name
  - Calculate average of min/max parameter pairs
  - MinMax normalize each parameter
  - Compute WQTI (weighted composite index)
        ↓
Missing Value Handling:
  - Linear interpolation within district + state groups
  - Group-wise mean imputation for remaining NaNs
        ↓
         ┌──────────────────────┬──────────────────────┐
         ↓                      ↓                      
   LSTM Model             Linear Regression       
   (TensorFlow/Keras)     (Scikit-learn)          
         ↓                      ↓                      
   Predictions 2021–2030   Predictions 2021–2030   
         ↓                      ↓                      
         └──────────────────────┘
                   ↓
       Mann-Kendall Trend Test
       (p-value, slope, trend direction)
                   ↓
       District-wise Results Table
       + Time-series Visualisations
       + Bar Charts (Increasing / Decreasing / No Trend)
```

---

## 🤖 LSTM Model Details

| Parameter | Value |
|---|---|
| Architecture | Sequential: LSTM(32, relu) → Dense(1) |
| Time steps | 3 years lookback |
| Optimizer | Adam |
| Loss | Mean Squared Error |
| Callback | EarlyStopping (patience=10, restore_best_weights=True) |
| Train/Test split | 80/20 per district |
| Prediction range | 2021–2030 |

---

## 📊 Mann-Kendall Statistical Test

After predicting WQTI values, a **Mann-Kendall trend test** is applied to each district's time series to determine whether the trend is statistically significant:

- **p < 0.05** → Trend is statistically significant
- **Trend direction** → Increasing / Decreasing / No Trend
- **Slope (Sen's slope)** → Rate of change per year

This adds research-grade statistical rigor beyond simple model predictions.

---

## 📈 Outputs

- **Historical WQTI table** → District-wise values with trend and p-value
- **Predicted WQTI table (2021–2030)** → Future projections with trend classification
- **Time-series plots** → Historical + predicted WQTI per district
- **Bar charts** → District-wise trend classification (Increasing / Decreasing / No Trend) per state

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.10 | Core language |
| TensorFlow / Keras | LSTM model |
| Scikit-learn | Linear Regression + MinMaxScaler |
| Pandas / NumPy | Data processing |
| Matplotlib / Seaborn | Visualisation |
| pymannkendall | Mann-Kendall trend testing |
| Google Colab | Development environment |

---

## 🚀 How to Run

### 1. Clone the repo
```bash
git clone https://github.com/Galaxy-03/groundwater-quality-prediction.git
cd groundwater-quality-prediction
```

### 2. Install dependencies
```bash
pip install pandas numpy matplotlib seaborn tensorflow scikit-learn pymannkendall
```

### 3. Add dataset
Place `7084_source_data.csv` in the project root (or update the filepath in the notebook).

### 4. Run notebook
Open `WQTI.ipynb` in Google Colab or Jupyter and run all cells.

---

## 📂 Repository Structure

```
groundwater-quality-prediction/
├── WQTI.ipynb              ← Main notebook (LSTM + Linear Regression + Mann-Kendall)
├── README.md               ← This file
└── requirements.txt        ← Python dependencies
```

---

## 🔍 Key Findings

- Applied Mann-Kendall trend test across districts — results showed statistically significant trends (p < 0.05) in multiple districts across both states
- LSTM outperformed Linear Regression in capturing non-linear degradation patterns
- BOD and Total Coliform showed highest contribution to WQTI variation
---

## 👩‍💻 Author

**Chinmayi Suhas Dighe**
B.Tech CSE, D Y Patil International University, Pune
