# bot.py

# Single-file MT5 trading bot for XAUUSD M1 using Wave Trend signals, SL/TP, logging, and emoji logs

import time
import datetime
import MetaTrader5 as mt5
import numpy as np

# 1. Configuration

SYMBOL        = "XAUUSDm"
TIMEFRAME     = mt5.TIMEFRAME\_M1
SL\_PIPS       = 3
TP\_PIPS       = 5
VOLUME        = 0.01
DEVIATION     = 10
MAGIC         = 234000
FETCH\_COUNT   = 250      # number of bars per fetch (enough for wave trend)

# Wave Trend parameters

WT\_N1 = 14  # channel length
WT\_N2 = 21  # average length for signal
WT\_AVG = 4  # smoothing for signal line

# 2. MT5 Connection

def init\_mt5():
if not mt5.initialize():
raise RuntimeError("❌ Failed to initialize MT5")
print("✅ MT5 initialized")

def shutdown\_mt5():
mt5.shutdown()
print("🔒 MT5 shutdown")

# 3. Data Fetching

def fetch\_rates(count=FETCH\_COUNT):
rates = mt5.copy\_rates\_from\_pos(SYMBOL, TIMEFRAME, 0, count)
if rates is None or len(rates) < count:
raise RuntimeError("❌ Failed to fetch rates or insufficient data")
return rates

# 4. Indicators

def ema(series, period):
weights = np.exp(np.linspace(-1., 0., period))
weights /= weights.sum()
return np.convolve(series, weights, mode="valid")

def sma(series, period):
return np.convolve(series, np.ones(period)/period, mode="valid")

# Wave Trend calculation

def wave\_trend(high, low, close):
\# typical price (hlc3)
ap = (high + low + close) / 3
esa = ema(ap, WT\_N1)
d = ema(np.abs(ap\[-len(esa):] - esa), WT\_N1)
ci = (ap\[-len(esa):] - esa) / (0.015 \* d)
tci = ema(ci, WT\_N2)
wt1 = tci
wt2 = sma(wt1, WT\_AVG)
return wt1, wt2

# 5. Signal Logic using Wave Trend

def check\_signal():
rates = fetch\_rates()
high = rates\['high']
low = rates\['low']
close = rates\['close']
wt1, wt2 = wave\_trend(high, low, close)
\# align lengths
\# buy when wt1 crosses above wt2
if wt1\[-2] < wt2\[-2] and wt1\[-1] > wt2\[-1]:
return "BUY", close\[-1]
\# sell when wt1 crosses below wt2
if wt1\[-2] > wt2\[-2] and wt1\[-1] < wt2\[-1]:
return "SELL", close\[-1]
return None, close\[-1]

# 6. Trading Execution

def execute\_trade(signal, price):
info = mt5.symbol\_info(SYMBOL)
if info is None:
raise RuntimeError(f"❌ Symbol {SYMBOL} not found in MT5")
point = info.point

```
if signal == "BUY":
    sl_price = price - SL_PIPS * point
    tp_price = price + TP_PIPS * point
    order_type = mt5.ORDER_TYPE_BUY
else:
    sl_price = price + SL_PIPS * point
    tp_price = price - TP_PIPS * point
    order_type = mt5.ORDER_TYPE_SELL

request = {
    "action":      mt5.TRADE_ACTION_DEAL,
    "symbol":      SYMBOL,
    "volume":      VOLUME,
    "type":        order_type,
    "price":       price,
    "sl":          sl_price,
    "tp":          tp_price,
    "deviation":   DEVIATION,
    "magic":       MAGIC,
    "comment":     "python bot",
    "type_time":   mt5.ORDER_TIME_GTC,
    "type_filling":mt5.ORDER_FILLING_IOC,
}
return mt5.order_send(request)
```

# 7. Logging

def log\_trade(signal, price, result):
now = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
status = "✅" if result.retcode == mt5.TRADE\_RETCODE\_DONE else "❌"
line = f"{now} | {SYMBOL} | {signal} | price={price:.2f} | SL={SL\_PIPS}pips | TP={TP\_PIPS}pips | {status}\n"
with open("trades.txt", "a") as f:
f.write(line)

def log\_action(msg):
print(msg)

# 8. Main Loop (runs every 10 seconds)

def main():
init\_mt5()
try:
while True:
sig, price = check\_signal()
if sig:
log\_action(f"📊 {sig} at {price:.2f}")
res = execute\_trade(sig, price)
log\_trade(sig, price, res)
if res.retcode == mt5.TRADE\_RETCODE\_DONE:
log\_action("🎉 Trade executed successfully!")
else:
log\_action(f"⚠️ Trade failed, retcode={res.retcode}")
else:
log\_action(f"⏳ No signal at {price:.2f}")
time.sleep(10)
except Exception as e:
print(f"🔥 Error: {e}")
finally:
shutdown\_mt5()

if **name** == "**main**":
main()
