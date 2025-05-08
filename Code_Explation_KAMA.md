
# 🔁 ZeroNet BTC Strategy – Quantitative Momentum + Volatility Trading Bot

A custom-built quantitative trading strategy using **Python**, focused on trading Bitcoin using **4-hour candles** from 2019 to 2023. The strategy leverages adaptive indicators like **Kaufman's Adaptive Moving Average (KAMA)**, **RSI**, and **volume filters**, with clearly defined long/short entry-exit conditions, adaptive stop-loss, and post-trade optimization.

---

## 📌 Project Overview

This project was created to **design, test, and analyze** a rule-based BTC trading system using backtested historical data. The strategy is built to handle various market regimes by dynamically adjusting to volatility and trend strength.

- **Backtesting period:** 2019–2023 (BTC/USDT 4H candles)
- **Language & Tools:** Python, Pandas, NumPy, Matplotlib
- **File Output:** `BTC_ZeroNet_Signals_dhram2.csv` containing full trade logs and signal data

---

## 🧠 Strategy Logic (ZeroNet)

### Entry Conditions

#### Long Entry:
- `Close > KAMA` (trend confirmation)
- `Close > SMA(60)` (momentum filter)
- `Efficiency Ratio (ER) > 0.4` (clear directional movement)
- `RSI between 40–70` (avoid overbought/oversold extremes)
- `Volume > 1.1 * Volume MA(20)` (volume spike detection)

#### Short Entry:
- `Close < KAMA`
- `Close < SMA(60)`
- `ER > 0.4`
- `RSI between 30–60`
- `Volume > 1.1 * Volume MA(20)`

### Exit Conditions
- **Stop-Loss Hit**:  
  - 2% from entry price (Long: `SL = 0.99 × Entry`; Short: `SL = 1.01 × Entry`)
- **Trend Reversal**:  
  - Exit if price crosses back below/above SMA
- **Over-extension Filter**:  
  - Exit if `ER > 0.75` (likely trend exhaustion)

### Signal Control
- Signals are flattened post-trade to avoid overlapping positions.
- Ensures clean signal generation using position tracking (`Position = 1, -1, or 0`).

---

## 📊 Performance Metrics (Backtested)

| Metric | Value |
|--------|-------|
| **Initial Capital** | $1000 |
| **Final Portfolio Value** | $26,744 |
| **Net Profit** | $4,316 (+431%) |
| **Max Drawdown** | 5.4% (simple), 15.6% (compound) |
| **Sharpe Ratio** | 8.05 |
| **Sortino Ratio** | 51.84 |
| **Win Rate** | 45% |
| **Number of Trades** | 40 |
| **Max Holding Time** | 69 days |
| **Average Time to Recovery** | 66.5 days |
| **Largest Win** | $1383 |
| **Average Win/Loss** | $289 / -$40 |
| **Total Fees Paid** | $643 |

---

## 🧾 Code Explanation (Line-by-Line)

### 1. Load Data
```python
data = pd.read_csv('BTC_2019_2023_4h.csv')
data['datetime'] = pd.to_datetime(data['datetime'])
data.set_index('datetime', inplace=True)
```
- Loads the BTC OHLCV data and converts the datetime column to a usable format for time-series operations.

---

### 2. Indicator Calculations

#### KAMA (Kaufman's Adaptive Moving Average) + Efficiency Ratio (ER)
```python
def kama_er(close, period=20, fast_ema=2, slow_ema=30):
    ...
```
- Custom implementation of KAMA with efficiency ratio to measure directional market movement and adjust smoothing dynamically.

#### RSI (Relative Strength Index)
```python
data['RSI'] = ...
```
- Rolling RSI calculated using a 14-period window to gauge momentum.

#### VWAP and Volume MA
```python
data['VWAP'] = ...
data['Volume_MA'] = data['volume'].rolling(20).mean()
```
- VWAP indicates average price weighted by volume. Volume MA helps detect volume spikes.

#### SMA
```python
data['SMA_60'] = data['close'].rolling(20).mean()
```
- 60-period SMA for long-term trend filtering.

---

### 3. Signal Generation Logic

#### Initialization
```python
data['signals'] = 0
data['Trade_Type'] = 'Hold'
data['Position'] = 0
```
- Initializes columns to track trades, type of signal, and current position.

#### Loop for Generating Trades
```python
for i in range(60, len(data)):
    ...
```
- Checks each candle after initial indicators are ready. Applies entry/exit logic based on current position, indicators, and stop-loss.

---

### 4. Post-Processing Signal Integrity
```python
for i in range(len(data)):
    if data['signals'].iloc[i] != 0:
        ...
```
- Ensures signals are cleaned and no overlapping positions exist. Helps in interpreting final strategy state.

---

### 5. Export Results
```python
output.to_csv('BTC_ZeroNet_Signals_dhram2.csv')
```
- Saves the final trading log to CSV with all important trade data.

---

## 📁 Output Files

- `BTC_ZeroNet_Signals_dhram2.csv`:  
  Contains full trading log with columns:
  - `signals`, `Trade_Type`, `Position`, `EntryPrice`, `StopLoss`, and OHLCV

---

## 📈 Visualization Ideas

Plot:
- BTC close price vs KAMA, SMA
- Entry/exit markers
- RSI subplot
- Volume spike detection

---

## 🚀 Future Enhancements

- Real-time deployment using Binance API
- Portfolio rebalancing and position sizing
- Dynamic stop-loss and trailing SL logic
- Integration with a performance dashboard

---

## 🤝 Contributions & Feedback

Feel free to fork, star, or raise issues. I’m actively enhancing the framework for future deployment.
