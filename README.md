# Stock Price Time Series Forecasting

> Codveda Data Science Internship — Task 3
> Real stock price analysis and ARIMA forecasting on live Yahoo Finance data.

**Intern:** Andabai Pinadowari Isaac
**GitHub:** [github.com/andabaipina](https://github.com/andabaipina)
**LinkedIn:** [linkedin.com/in/andabai-isaac-aa217030b](https://linkedin.com/in/andabai-isaac-aa217030b)

---

## What this project does

Pulls real historical stock prices from Yahoo Finance, analyses their behaviour across multiple techniques, builds an ARIMA forecasting model, and honestly evaluates whether it beats a naive baseline — which for stock prices is a more meaningful benchmark than it sounds.

Default ticker is **AAPL** (Apple Inc.). Change the `TICKER` variable to analyse any other stock symbol.

---

## Steps covered

### Step 1 — Load real stock data
- Fetched live historical daily closing prices using `yfinance` — no API key required
- Reindexed to business-day frequency and forward-filled holiday gaps
- Computed daily log returns and annualised volatility

### Step 2 — Explore the raw series
- Plotted the full closing price history
- Plotted daily log returns to visualise volatility clustering

### Step 3 — Decompose into trend, seasonality, residual
- Applied `seasonal_decompose` with a 21-day period (one trading month)
- **Important caveat documented in the notebook:** stock prices are close to a random walk and do not have the strong seasonal patterns that retail or weather data does. The seasonal component's share of total variance is printed explicitly so the result can be interpreted honestly rather than over-interpreted

### Step 4 — Moving averages and exponential smoothing
- Computed 20-day and 50-day simple moving averages (SMA) — standard technical analysis benchmarks
- Computed 20-day EWMA (weights recent observations more heavily than SMA)
- Fitted Holt-Winters exponential smoothing with additive trend and no seasonal component — matching what Step 3 revealed about weak seasonality

### Step 5 — Stationarity testing
- Ran the Augmented Dickey-Fuller (ADF) test on the raw price series
- Result: raw price is **not stationary** (as expected — stock prices trend)
- First difference of the price series **is stationary**
- This confirmed `d=1` as the differencing order for ARIMA

### Step 6 — ARIMA model selection and fitting
- Held out the last **30 trading days** as a test set — never seen during fitting
- Grid searched over `p ∈ {0,1,2,3}` and `q ∈ {0,1,2,3}` with `d=1` fixed
- Selected the order with the lowest AIC (rewards fit while penalising unnecessary complexity)
- Fitted the final ARIMA model on the training set only

### Step 7 — Forecast, evaluate, visualise
- Forecast 30 days forward with 95% confidence intervals
- Evaluated against two benchmarks:

| Model | RMSE | MAE |
|---|---|---|
| ARIMA (best order) | computed per run | computed per run |
| Naive baseline (last known price) | computed per run | computed per run |

- Plotted actual vs forecast with confidence bands

### Step 8 — Summary of findings
- Written summary of what the decomposition, stationarity test, and forecast results actually mean in context

---

## Key concepts learned

**Why stationarity matters**
ARIMA assumes the series has a constant mean and variance over time. Stock prices don't — they trend. First differencing (computing price changes rather than prices) removes the trend and produces a stationary series the model can work with. The ADF test confirms this mathematically rather than assuming it.

**What AIC actually measures**
AIC is not a measure of accuracy — it's a trade-off score. Lower AIC means the model explains the data well without using unnecessarily many parameters. A model with one extra parameter that barely improves fit will score worse on AIC than a simpler model. This is why grid searching on AIC produces more generalisable models than grid searching on training error.

**The naive baseline is the honest benchmark**
For stock prices, "tomorrow's price = today's price" is a surprisingly strong forecast. This is what the efficient market hypothesis predicts — if prices fully reflect available information, no model should consistently beat random walk prediction. Comparing ARIMA against this baseline is more meaningful than comparing it against nothing. An ARIMA that barely beats naive has added little real forecasting value.

**Forward-fill vs interpolation for trading gaps**
Reindexing to business days creates gaps for holidays. Forward-filling (carrying the last known price forward) is the standard approach because the price genuinely didn't change on a holiday — the market was closed. Interpolating would invent price movements that didn't happen.

**Why the seasonal component is unreliable for stocks**
Seasonal decomposition forces a seasonal component whether or not one genuinely exists. For genuinely seasonal data (retail sales, electricity demand, temperatures), this component is meaningful. For stock prices, it mostly captures noise. The notebook prints the seasonal variance share so this can be evaluated quantitatively rather than assumed.

---

## Files

| File | Description |
|---|---|
| `Stock_TimeSeries_Forecasting.ipynb` | Full notebook — all steps with explanations |
| `raw_series.png` | Full closing price history |
| `log_returns.png` | Daily log returns showing volatility |
| `decomposition.png` | Trend, seasonal, residual breakdown |
| `moving_averages.png` | SMA 20, SMA 50, EWMA overlay |
| `holt_winters.png` | Holt-Winters fitted values |
| `arima_forecast.png` | 30-day forecast vs actual with confidence bands |

---

## Tools

| Tool | Purpose |
|---|---|
| `yfinance` | Live stock data fetching from Yahoo Finance |
| `pandas` | Time series indexing, resampling, feature creation |
| `NumPy` | Log return calculation, volatility |
| `statsmodels` | Seasonal decomposition, ADF test, ARIMA |
| `sklearn` | RMSE and MAE evaluation |
| `matplotlib` | All visualisations |

---

## How to run

1. Open `Stock_TimeSeries_Forecasting.ipynb` in Google Colab
2. Run all cells in order
3. Change `TICKER = "AAPL"` in Step 1 to any valid Yahoo Finance ticker to analyse a different stock

No API keys required. No data upload needed — `yfinance` fetches live data automatically.

---

## Broader context

This project is part of a growing portfolio built during the Codveda Data Science internship, alongside:

- **Task 1** — Web scraping (BeautifulSoup · requests · 1,000 records across 50 pages)
- **Task 2** — SVM classification (Iris dataset · 100% test accuracy · AUC 1.000)
- **Task 4** — Neural network for telecom churn prediction (TensorFlow · Keras · class imbalance handling)

Independent projects also include an [interactive Nigeria economic dashboard](https://andabaipina.github.io/nigeria-economic-dashboard/) with a 30-year ML simulation, housing price prediction, NBA performance analysis, and NLP sentiment analysis.

---

*Codveda Data Science Internship · Andabai Pinadowari Isaac · github.com/andabaipina*
](https://github.com/andabaipina/Time_Series_Forecasting_Codveda)
