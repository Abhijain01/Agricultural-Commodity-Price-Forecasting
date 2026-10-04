# 🌾 Way2Agribusiness Price Forecasting

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/)
[![Model](https://img.shields.io/badge/Model-Holt--Winters%20Exponential%20Smoothing-brightgreen.svg)](https://www.statsmodels.org/)
[![Test MAPE](https://img.shields.io/badge/Test%20MAPE-9.85%25-success.svg)](#model-performance--comparison)
[![Status](https://img.shields.io/badge/Status-Completed%20%26%20Deployed-orange.svg)](#deployment--architecture-roadmap)

An end-to-end time series price forecasting system built for **Way2Agribusiness**. This project analyzes **20 years of historical monthly commodity price data (Jan 2005 – Sep 2025)**, benchmarks multiple predictive models (Holt-Winters, XGBoost, Facebook Prophet), and delivers a **12-month future price forecast (Oct 2025 – Sep 2026)** with actionable procurement and pricing strategies.

---

## 📌 Executive Summary

- **Primary Goal**: Predict future commodity prices to enable proactive procurement, hedge against price spikes, and protect profit margins.
- **Dataset**: 249 consecutive months (Jan 2005 – Sep 2025), zero missing values.
- **Key Insight**: The market experienced a structural shift in Dec 2023 (+92.4% spike) and settled into a **new high price regime** (₹15,000 – ₹16,000). Prices will **not** return to pre-2023 historical levels (₹7,000 – ₹8,000).
- **Winning Model**: **Holt-Winters Exponential Smoothing** (Additive Trend & Additive Seasonality).
- **Test Performance**: **9.85% MAPE** (Mean Absolute Percentage Error) on out-of-sample data with no overfitting (train/test gap of only 4.35%).

---

## 📊 Dataset Overview

The dataset contains monthly average agricultural commodity prices recorded over 20 years.

| Metric | Value |
| :--- | :--- |
| **Total Rows** | 249 months |
| **Date Range** | Jan 2005 – Sep 2025 |
| **Missing Values** | 0 (Complete dataset) |
| **Starting Price (Jan 2005)** | ₹5,500 |
| **Ending Price (Sep 2025)** | ₹15,750 |
| **Minimum Price** | ₹3,500 (Apr 2006) |
| **Maximum Price** | ₹16,163 (Jul 2024) |
| **Mean / Median Price** | ₹7,918.9 / ₹7,250.0 |
| **Standard Deviation** | ₹2,805.0 |

### Extreme Market Shocks & Outlier Analysis
- **Dec 2023 Spike**: +92.42% single-month surge.
- **Jun 2021 Drop**: -53.33% single-month drop.
- **Decision**: Outliers were **retained**, as they represent genuine macroeconomic/supply-chain shocks rather than data errors.

---

## 🔬 Exploratory Data Analysis & Decomposition

### 1. Trend & Seasonality Decomposition (Multiplicative Model)
- **Long-term Trend**: Price increased **+174.6%** over the 20-year period (₹5,010 → ₹13,759 trend baseline).
- **Seasonal Cycle (Period = 12 months)**:
  - 📈 **Peak Demand Months**: **May (+3.7%)**, **Mar (+2.4%)**, **Dec (+1.7%)** — driven by pre-summer demand and festival surges.
  - 📉 **Post-Harvest Supply Dips**: **Jul (-3.8%)**, **Jun (-3.0%)**, **Aug (-2.7%)** — driven by fresh seasonal harvest arrivals.

### 2. Stationarity Test (Augmented Dickey-Fuller)
- **Raw Price Series**: ADF Statistic = -1.6311, **p-value = 0.4669** $\rightarrow$ **NON-STATIONARY** (Strong upward trend).
- **First Difference ($\Delta y_t$)**: ADF Statistic = -16.1384, **p-value = 0.0000** $\rightarrow$ **STATIONARY** ($d=1$).

---

## 🏆 Model Benchmarking & Performance Comparison

We evaluated multiple modeling paradigms ranging from traditional statistical models to machine learning and specialized time-series frameworks on a 80/20 train/test split.

| Model | Technique | Train MAPE | Test MAPE | Overfit Gap | Status |
| :--- | :--- | :---: | :---: | :---: | :--- |
| **Holt-Winters** 🥇 | **Additive Trend & Seasonality** | **5.50%** | **9.85%** | **4.35%** | **SELECTED WINNER** |
| **XGBoost** | Lag + Rolling Window Features | 5.37% | 30.97% | 25.60% | Overfitting on small N |
| **FB Prophet** | Additive Decomposition | — | 49.14% | — | Failed to adapt to new regime |

### Why Holt-Winters Won:
1. **Small Sample Efficiency**: 249 monthly points is ideal for exponential smoothing parameters without parameter explosion.
2. **Regime Shift Robustness**: Level ($\alpha$), trend ($\beta$), and seasonality ($\gamma$) smoothing parameters quickly adapt to recent baseline shifts compared to decision-tree models.
3. **Overfit Prevention**: Only a 4.35% gap between train and test performance.

---

## 🔮 12-Month Price Forecast (Oct 2025 – Sep 2026)

Using the finalized Holt-Winters model saved in `holt_winters_model.pkl`:

| Month | Forecasted Price (₹) | Lower 95% CI (₹) | Upper 95% CI (₹) | Phase |
| :--- | :---: | :---: | :---: | :--- |
| **Oct 2025** | ₹15,200 | ₹12,035 | ₹18,365 | Stable |
| **Nov 2025** | ₹15,219 | ₹12,054 | ₹18,384 | Stable |
| **Dec 2025** | ₹15,347 | ₹12,182 | ₹18,512 | Moderate Surge |
| **Jan 2026** | **₹15,131** | ₹11,966 | ₹18,296 | 📉 **Lowest Month** |
| **Feb 2026** | ₹15,372 | ₹12,207 | ₹18,537 | Rising |
| **Mar 2026** | ₹15,588 | ₹12,423 | ₹18,753 | High Demand |
| **Apr 2026** | ₹15,402 | ₹12,237 | ₹18,567 | Moderate |
| **May 2026** | ₹15,760 | ₹12,595 | ₹18,925 | Pre-Summer Peak |
| **Jun 2026** | ₹15,522 | ₹12,357 | ₹18,687 | Steady |
| **Jul 2026** | ₹15,560 | ₹12,395 | ₹18,725 | Steady |
| **Aug 2026** | ₹15,666 | ₹12,501 | ₹18,831 | Accumulation |
| **Sep 2026** | **₹15,845** | ₹12,680 | ₹19,010 | 📈 **Highest Month** |

- **12-Month Average Forecast**: **₹15,468**
- **Confidence Interval**: **$\pm$ ₹3,165 (95% CI)**

---

## 💡 Strategic Business Recommendations

Based on the 12-month trajectory, Way2Agribusiness should execute the following strategy:

### 1. Procurement Strategy
- **Lock Forward Contracts Immediately**: Secure contracts at current rates before additional incremental rises.
- **Optimal Stock-up Window**: Bulk purchase in **Jan 2026** (forecasted lowest price point at ₹15,131).
- **Avoid Spot Market Purchases**: Minimize spot buying in **Mar 2026** & **Sep 2026** when peak prices occur.

### 2. Pricing & Margin Protection
- **Update Base Selling Prices**: Adjust catalog pricing to reflect the new ₹15,000+ baseline.
- **Offer Fixed 6-Month Contracts**: Offer fixed-price commitments to key corporate buyers through Q1/Q2 2026.
- **Contract Duration Limit**: Avoid committing to fixed pricing beyond Sep 2026 due to widening confidence intervals.

### 3. Inventory Management
- Expand buffer inventory from 30 days to **60–90 days** ahead of peak demand months (March & May).

### 4. Key Performance Indicators (KPIs)
- **Forecast Precision**: Track monthly error (target: $< 10\%$ MAPE).
- **Procurement Efficiency**: Goal is to outperform market spot price by at least **9.85%** via strategic timing.

---

## 📂 Repository Structure

```text
way2agri/
├── Way2Agribusiness_Price_Forecasting.ipynb  # Primary data science notebook (EDA, Models, Forecast)
├── way2agri_human.ipynb                      # Cleaned notebook version with implementation details
├── price_data.csv                            # Historical monthly commodity price dataset (2005–2025)
├── holt_winters_model.pkl                    # Pickled final trained Holt-Winters model artifact
├── forecast.png                              # Saved forecast visualization chart
├── price_inspection.png                      # Time-series inspection & outlier chart
└── README.md                                 # Project documentation
```

---

## 🚀 Deployment & Integration Blueprint

### 1. Model Persistence & Ingestion
To load and generate predictions in Python:
```python
import joblib

# Load trained model
model = joblib.load('holt_winters_model.pkl')

# Forecast next 12 steps
forecast = model.forecast(12)
print(forecast)
```

### 2. API Backend Architecture (Django + FastAPI)

- **FastAPI / Django View Endpoint**:
```python
from fastapi import FastAPI
import joblib

app = FastAPI()
model = joblib.load('holt_winters_model.pkl')

@app.get('/api/predict')
def get_forecast():
    forecast = model.forecast(12)
    return {
        "status": "success",
        "months": [str(d.date()) for d in forecast.index],
        "prices": [round(val, 2) for val in forecast.values]
    }
```

### 3. Client Frontend Integration (Next.js & React Native)
- **Web Dashboard (Next.js)**: Displays dynamic price projections via Recharts or Chart.js.
- **Mobile App (React Native)**: Fetches JWT-authenticated forecasts for real-time buyer notifications.

### 4. Production Monitoring & Automated Retraining
- **Drift Detection**: Monitor rolling 3-month MAPE. If actual price deviates $>10\%$ from forecast for 3 consecutive months, trigger automated retraining.
- **Monthly Pipeline**: Schedule monthly model updates using **Celery** or **Airflow** as new monthly prices are ingested into `price_data.csv`.

---

## ⚙️ Setup & Installation

1. **Clone the repository**:
   ```bash
   git clone https://github.com/your-org/way2agri.git
   cd way2agri
   ```

2. **Create and activate a virtual environment**:
   ```bash
   python -m venv venv
   # On Windows:
   venv\Scripts\activate
   # On macOS/Linux:
   source venv/bin/activate
   ```

3. **Install required dependencies**:
   ```bash
   pip install pandas numpy statsmodels xgboost prophet joblib matplotlib seaborn
   ```

4. **Launch Jupyter Notebook**:
   ```bash
   jupyter notebook Way2Agribusiness_Price_Forecasting.ipynb
   ```

---

## 📄 License & Attribution

Developed for **Way2Agribusiness**. All price data and forecast models are maintained for agricultural commodity market intelligence.
