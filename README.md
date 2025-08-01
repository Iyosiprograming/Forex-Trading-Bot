
forex_trading_bot/
│
├── main.py                        # Entry point – run this to start the bot
│
├── config/
│   └── settings.py                # Configs: lot size, default EMAs, SL/TP, trailing rules
│
├── core/
│   ├── __init__.py
│   ├── broker.py                  # Connect to MetaTrader5 (or other broker APIs)
│   ├── strategy.py                # EMA crossover logic
│   ├── trader.py                  # Handles open/close trade, SL/TP, trailing SL
│   ├── signals.py                 # Detect buy/sell signals
│   └── data.py                    # Fetch market data, candles, indicators
│
├── utils/
│   ├── logger.py                  # Logging all trades and signals
│   ├── timer.py                   # Handles looping every X seconds or minutes
│   └── notifier.py                # (Optional) Send messages via Telegram/email
│
├── logs/
│   └── trade_log.txt              # Logs of executed trades
│
├── requirements.txt               # All dependencies (e.g., MetaTrader5, pandas)
└── README.md                      # Project overview, setup instructions
