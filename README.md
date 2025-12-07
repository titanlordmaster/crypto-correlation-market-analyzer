Here’s a full replacement `README.md` you can drop in:

# Crypto Market Data Capstone  
**Correlation and Inter-Market Analysis of Bitcoin, Ethereum, and Cardano on Binance**

This repository contains my Master’s Data Analytics capstone project, focused on
understanding how major cryptocurrencies move together and whether those
relationships can be used to inform trading and risk-management strategies.

The core question:

> **What is the nature of the relationship between Bitcoin, Ethereum, and Cardano on Binance, and can these correlations be leveraged to optimize trading strategies?**

---

## Project Overview

- **Assets:** BTC/USDT, ETH/USDT, ADA/USDT  
- **Exchange:** Binance  
- **Frequency:** 1-minute OHLCV (Open, High, Low, Close, Volume)  
- **Horizon:** ~2 years of historical data (ending at “yesterday’s midnight”)  
- **Tools & Stack:**  
  - Python, Jupyter Notebooks  
  - `ccxt` (Binance API client)  
  - `pandas`, `numpy`  
  - `matplotlib`, `seaborn`  
  - `mplfinance` (candlesticks, Bollinger Bands, Renko, RSI)

The project is structured in three main parts:

1. **Data extraction & cleaning** – pulling 1-minute OHLCV candles from Binance and
   building a consistent minute-by-minute time series.
2. **Analysis & visualization** – correlation analysis, rolling volatility, and
   technical charts.
3. **Reporting & reflection** – written report, action plan, and slide deck.

---

## Research Question & Hypotheses

**Research Question**

> *What is the nature of the relationship between Bitcoin, Ethereum, and Cardano on Binance, and can these correlations be leveraged to optimize trading strategies?*

**Hypotheses**

- **Null (H₀):** There is no statistically significant correlation between the
  price movements of BTC, ETH, and ADA on Binance.
- **Alternate (H₁):** There is a statistically significant correlation among
  these assets, with evidence of predictive relationships (e.g., one asset
  acting as a leading indicator for others).

---

## Data & Methodology

### 1. Data Collection

- Data is sourced directly from the **Binance API**, accessed through
  a custom Python client built on **`ccxt`**.
- For each symbol (`BTCUSDT`, `ETHUSDT`, `ADAUSDT`), 1-minute OHLCV candles are
  requested in a **round-robin** fashion.
- Batches are **saved incrementally** to CSV files so that:
  - API rate limits / connection issues don’t cause total data loss.
  - Partial data can be inspected early and re-runs are incremental.

### 2. Extraction & Cleaning

Implemented in **`marketDataPullerCleaner.ipynb`**:

- Load raw CSVs (e.g., `binance_klines_BTCUSDT_2years.csv`) into pandas.
- Convert exchange timestamps to timezone-aware `datetime` objects.
- Set timestamp as the DataFrame index.
- Generate a **complete 1-minute date range** between the min and max timestamps.
- Reindex to this full range to:
  - Identify missing rows (e.g., ADA had over 1,048,160 expected rows with ~40% missing).
  - Quantify data gaps per symbol.
- **Cleaning decision:**  
  - Drop rows that are **fully empty** (all columns `NaN`), keeping only rows representing actual trading data.
  - This focuses the analysis on periods where the market is actively trading.

### 3. Feature Engineering

Implemented across the notebooks:

- **Percentage change (`pct_change`)** of closing price from one minute to the next.
- **Movement labels (`movement`)**:
  - `"up"`, `"down"`, `"flat"` based on thresholds applied to `pct_change`.
- These features normalize price scale differences and make cross-asset comparisons straightforward.

### 4. Analysis & Visualization

Implemented in **`marketVisualizer.ipynb`**:

- **Time Series Analysis**
  - Line plots of raw closing prices.
  - Line plots of percentage changes (`pct_change`) to visualize normalized movement.
- **Correlation Analysis**
  - Merge per-symbol data on timestamp.
  - Compute correlation matrix on `pct_change` across BTC, ETH, ADA.
  - Visualize as a heatmap for interpretability.
- **Rolling Analysis**
  - Rolling standard deviation (volatility) of `pct_change` using a 60-minute window.
  - Rolling correlations to see how relationships evolve through time.
- **Technical Analysis Visuals (via `mplfinance`)**
  - Candlestick charts (with volume and moving averages).
  - Bollinger Bands overlays.
  - Renko charts.
  - RSI (Relative Strength Index) subplots.
  - Additional line charts (e.g., ADA with `pct_change` overlay).

---

## Key Findings (High-Level)

- **Strong correlations**
  - **BTC vs ETH:** ~0.84 (high positive correlation)  
  - **BTC vs ADA:** ~0.80  
  - **ETH vs ADA:** ~0.68  
- **Interpretation**
  - BTC and ETH generally move together; BTC’s movement is a strong signal for ETH.
  - ADA is somewhat less tightly linked but still meaningfully correlated.
  - BTC can reasonably be treated as a **leading indicator** for ETH and, to some
    extent, ADA.

- **Volatility**
  - Rolling volatility highlights periods of elevated risk and instability.
  - These windows are prime candidates for dynamic risk management strategies
    (position sizing, stop-loss tuning, etc.).

- **Trading Implications**
  - Monitor BTC as a primary signal and adjust exposure in ETH/ADA accordingly.
  - Combine correlation structure + technical indicators (RSI, Bollinger Bands,
    Renko) for more nuanced entry/exit decisions.

---

## Limitations & Future Work

**Limitations**

- Approx. **40% of expected 1-minute rows** for some assets (like ADA) are
  missing; fully empty rows were dropped:
  - This may bias the analysis away from low-volume periods.
- Classical techniques (correlation, rolling stats) assume conditions
  (e.g., stationarity) that may not fully hold in crypto markets.
- Technical indicators are sensitive to **parameter choices** (window sizes,
  thresholds), which were set using reasonable defaults rather than formal
  optimization.

**Potential Extensions**

- Extend the asset universe (more altcoins, stablecoins, or indices).
- Incorporate **machine learning models** (e.g., LSTM or sequence models) to
  predict returns/volatility using:
  - Past prices  
  - Engineered features  
  - Cross-asset signals
- Wrap the analysis into an **AI assistant** or dashboard that:
  - Answers questions about current correlations and volatility.
  - Suggests risk adjustments based on live data.

---

## Repository Structure

Key files in this repo:

- **Notebooks**
  - `marketDataPullerCleaner.ipynb`  
    → Data extraction from Binance, CSV writing, reindexing, and core cleaning.
  - `marketVisualizer.ipynb`  
    → Exploratory data analysis, correlation and rolling metrics, and technical charts.

- **Reports**
  - `capstonetask2.pdf`  
    → Full Data Analytics Report & Executive Summary, suitable for academic and
      business stakeholders.
  - `capstonetask2.odt`  
    → Editable version of the analytics report.
  - `capstone3.odt`  
    → Action-oriented discussion and reflection, including proposed next steps and
      expected business impact.
  - `TASK3CAPSTONE.odp`  
    → Presentation slide deck summarizing the project for a non-technical audience.

- **Meta**
  - `README.md`  
    → This file.
  - `LICENSE`  
    → Project licensing information.

---

## How to Run the Project

1. **Clone the repo**

   ```bash
   git clone <this-repo-url>
   cd Crypto-Market-Data-Capstone


2. **Create and activate a virtual environment (recommended)**

   ```bash
   python -m venv .venv
   source .venv/bin/activate  # Windows: .venv\Scripts\activate
   ```

3. **Install dependencies**

   ```bash
   pip install -r requirements.txt
   ```

   > If no `requirements.txt` is present yet, typical dependencies include:
   > `ccxt`, `pandas`, `numpy`, `matplotlib`, `seaborn`, `mplfinance`, `jupyter`.

4. **Run the notebooks**

   ```bash
   jupyter notebook
   ```

   * Open and run `marketDataPullerCleaner.ipynb` to:

     * Re-pull or clean data (if needed).
   * Then open `marketVisualizer.ipynb` to:

     * Reproduce the analysis, plots, and correlation results.

---

## Usage & Adaptation

You can reuse this project as a template for:

* Analyzing correlations and volatility across other crypto pairs or markets.
* Building a foundation for algorithmic trading research.
* Extending into an AI/LLM assistant that answers questions about market
  structure and dynamics in real time.

If you adapt or extend this work (e.g., new assets, ML models, or an AI copilot
on top of the metrics), consider forking the repo and documenting your changes
in a new section of the README.
