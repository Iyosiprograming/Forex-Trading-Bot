# XAUUSD M1 EMA+RSI MT5 Trading Bot

A simple Python-based trading bot for MetaTrader 5 that:

* **Connects** to MT5 and fetches 1-minute (M1) XAUUSD data.
* **Calculates** EMA (20) and RSI (14) indicators to generate BUY/SELL signals.
* **Executes** market orders with a fixed Stop Loss (3 pips) and Take Profit (5 pips).
* **Logs** each trade to `trades.txt` (datetime, symbol, direction, price, SL/TP, status).
* **Prints** all actions and results in the terminal with emojis for easy readability.
* **Runs** continuously, checking every 10 seconds.

---

## 📦 Prerequisites

* Python 3.7+
* MetaTrader 5 client installed and logged in to a broker account
* Python packages:

  ```bash
  pip install MetaTrader5 numpy
  ```

---

## 🗂️ File Structure

```
├─ bot.py           # Single-file trading bot
├─ trades.txt      # Generated log of all executed trades
└─ README.md       # Project documentation
```

---

## ⚙️ Configuration

In `bot.py`, adjust the following constants as needed:

| Variable      | Description                                  | Default     |
| ------------- | -------------------------------------------- | ----------- |
| `SYMBOL`      | MT5 symbol (XAUUSD M1)                       | `"XAUUSDm"` |
| `EMA_PERIOD`  | EMA lookback period                          | `20`        |
| `RSI_PERIOD`  | RSI lookback period                          | `14`        |
| `OVERSOLD`    | RSI oversold threshold                       | `30`        |
| `OVERBOUGHT`  | RSI overbought threshold                     | `70`        |
| `SL_PIPS`     | Stop Loss in pips                            | `3`         |
| `TP_PIPS`     | Take Profit in pips                          | `5`         |
| `VOLUME`      | Lot size                                     | `0.01`      |
| `FETCH_COUNT` | Number of bars to fetch per iteration        | `100`       |
| `DEVIATION`   | Maximum price deviation (slippage) in points | `10`        |
| `MAGIC`       | Magic number for order identification        | `234000`    |

---

## 🚀 Usage

1. Place `bot.py` and this `README.md` in the same folder.
2. Install dependencies (see Prerequisites).
3. Open your MT5 terminal and ensure it's running under the same Windows user.
4. Run:

   ```bash
   python bot.py
   ```
5. Watch the terminal for emoji‑rich status updates.
6. Check `trades.txt` for a history of executed trades.

---

## 🛠️ Troubleshooting

* **MT5 initialization fails**: verify MT5 is installed and you’re logged in.
* **Data fetch errors**: confirm symbol name and timeframe; increase `FETCH_COUNT` if needed.
* **Order send errors**: check available account balance and volume limits.

---

## 📄 License

MIT License © 2025
