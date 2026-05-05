# 📺 TV Scraper

> Fetch historical price data from TradingView in any format you need.

[![PyPI version](https://img.shields.io/badge/pypi-v0.1.0-blue)](https://pypi.org/project/tv-scraper-py/)
[![Python](https://img.shields.io/badge/python-3.8+-blue)](https://www.python.org/)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![GitHub stars](https://img.shields.io/badge/dynamic/json?label=stars&query=%24.stargazers_count&url=https://api.github.com/repos/anuragjha0001/tv-scraper-py)](https://github.com/anuragjha0001/tv-scraper-py)

## 📖 Overview

**TV Scraper** is a lightweight yet powerful Python library that fetches historical OHLCV (Open, High, Low, Close, Volume) data from TradingView. Whether you need DataFrames for analysis, NumPy arrays for machine learning, or JSON for APIs — TV Scraper has you covered.

### Why TV Scraper?

- 🚀 **One line to get data** — `df = tv.get("BTCUSDT")`
- 🎨 **7 output formats** — pandas, numpy, arrays, dict, json, csv, raw tuples
- 🧹 **Zero bloat** — Only requires `websocket-client`
- 📊 **ML-ready** — Direct to TensorFlow, PyTorch, or scikit-learn
- 🔄 **Batch fetching** — Get multiple symbols in one call
- 🛡️ **Smart defaults** — Works out of the box, fully customizable
- 🌍 **All TradingView markets** — Crypto, stocks, forex, indices, commodities

---

## 📦 Installation

```bash
# Basic (DataFrame support)
pip install tv-scraper-py[pandas]

# ML support (NumPy arrays)
pip install tv-scraper-py[numpy]

# Everything
pip install tv-scraper-py[all]

# Latest from GitHub
pip install git+https://github.com/anuragjha0001/tv-scraper-py.git
```

---

## 🚀 Quick Start

```python
from tv_scraper import TvDatafeed

tv = TvDatafeed()
df = tv.get("BTCUSDT")
print(df.head())
```

**Output:**
```
                       open    high     low   close    volume
timestamp                                                   
2026-04-05 00:00:00  83500.0  84200.0  83400.0  84000.0   125.34
2026-04-06 00:00:00  84000.0  84800.0  83900.0  84600.0   200.50
```

---

## 📚 Usage Guide

### Basic Fetching
```python
from tv_scraper import TvDatafeed
from datetime import datetime

tv = TvDatafeed()

# Simple - last 30 days
df = tv.get("BTCUSDT")

# Custom parameters
df = tv.get(
    symbol="ETHUSDT",
    exchange="BINANCE",
    interval="1H",
    start="2024-01-01",
    end="2024-01-31",
)

# Using datetime objects
start = datetime(2024, 1, 1)
end = datetime.now()
df = tv.get("SOLUSDT", start=start, end=end, interval="4H")
```

### All Output Formats
```python
tv = TvDatafeed()

df = tv.get("BTCUSDT", output_format="pandas")                # DataFrame (default)
arr = tv.get("BTCUSDT", output_format="numpy")                # NumPy structured array
ts, o, h, l, c, v = tv.get("BTCUSDT", output_format="arrays") # ML-ready
data = tv.get("BTCUSDT", output_format="dict")                # List of dicts
json_str = tv.get("BTCUSDT", output_format="json", indent=2)  # JSON string
csv_str = tv.get("BTCUSDT", output_format="csv")              # CSV string
bars = tv.get("BTCUSDT", output_format="raw")                 # Raw tuples
```

### Batch Fetching
```python
tv = TvDatafeed()

results = tv.get_multi([
    {"symbol": "BTCUSDT", "interval": "1H", "output_format": "pandas"},
    {"symbol": "ETHUSDT", "interval": "1H", "output_format": "dict"},
    {"symbol": "SOLUSDT", "interval": "4H", "output_format": "json"},
])

btc_df = results["BINANCE:BTCUSDT"]
eth_data = results["BINANCE:ETHUSDT"]
```

### ML Pipeline (PyTorch)
```python
from tv_scraper import TvDatafeed
import numpy as np
import torch

tv = TvDatafeed()
ts, o, h, l, c, v = tv.get("BTCUSDT", interval="1H", output_format="arrays")

X = np.column_stack([o, h, l, v])[:-1]
y = np.roll(c, -1)[:-1]

X_tensor = torch.from_numpy(X).float()
y_tensor = torch.from_numpy(y).float()
```

### API Backend (FastAPI)
```python
from fastapi import FastAPI
from tv_scraper import TvDatafeed

app = FastAPI()
tv = TvDatafeed()

@app.get("/api/crypto/{symbol}")
def get_crypto(symbol: str, interval: str = "1D"):
    return tv.get(symbol.upper(), interval=interval, output_format="json")
```

### Stock & Forex Markets
```python
tv = TvDatafeed()

df = tv.get("RELIANCE", exchange="NSE")      # Indian Stocks
df = tv.get("AAPL", exchange="NASDAQ")        # US Stocks
df = tv.get("EURUSD", exchange="FX_IDC")      # Forex
df = tv.get("SPX", exchange="SP")             # Indices
df = tv.get("XAUUSD", exchange="OANDA")       # Commodities
```

### Context Manager
```python
with TvDatafeed() as tv:
    df = tv.get("BTCUSDT")
    # Connection auto-closed
```

---

## 🕐 Supported Timeframes

| Code | Timeframe | Code | Timeframe |
|------|-----------|------|-----------|
| `1m` | 1 Minute | `2H` | 2 Hours |
| `3m` | 3 Minutes | `3H` | 3 Hours |
| `5m` | 5 Minutes | `4H` | 4 Hours |
| `15m` | 15 Minutes | `1D` | Daily |
| `30m` | 30 Minutes | `1W` | Weekly |
| `1H` | 1 Hour | `1M` | Monthly |

---

## 🎯 Output Formats

| Format | Returns | Required | Best For |
|--------|---------|----------|----------|
| `pandas` | DataFrame | `pandas` | Analysis, plotting |
| `numpy` | Structured ndarray | `numpy` | Scientific computing |
| `arrays` | Tuple of 6 arrays | `numpy` | ML/AI pipelines |
| `dict` | List of dicts | None | APIs, databases |
| `json` | JSON string | None | HTTP responses |
| `csv` | CSV string | None | Excel, spreadsheets |
| `raw` | List of tuples | None | Custom processing |

---

## 🛠️ API Reference

### TvDatafeed Class
```python
TvDatafeed(
    auth_token="unauthorized_user_token",
    max_retries=3,
    timeout=10
)
```

### get() Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `symbol` | str | Required | Trading pair symbol |
| `exchange` | str | `"BINANCE"` | Exchange identifier |
| `interval` | str | `"1D"` | Timeframe interval |
| `start` | str/datetime | 30 days ago | Start date |
| `end` | str/datetime | now | End date |
| `output_format` | str | `"pandas"` | Output format |

### Date Formats
```python
tv.get("BTCUSDT", start="2024-01-01")            # YYYY-MM-DD
tv.get("BTCUSDT", start="01-01-2024")            # DD-MM-YYYY
tv.get("BTCUSDT", start="2024-01-01 12:30:00")   # With time
tv.get("BTCUSDT", start=datetime(2024, 1, 1))    # datetime object
tv.get("BTCUSDT", start=1704067200)              # Unix timestamp
```

---

## ❗ Error Handling

```python
from tv_scraper import TvDatafeed
from tv_scraper.exceptions import NoDataError, ConnectionError, FormatError

tv = TvDatafeed()

try:
    df = tv.get("INVALID_SYMBOL")
except NoDataError:
    print("No data for this symbol")
except ConnectionError:
    print("Could not connect")
except FormatError:
    print("Invalid output format")
```

---

## 🧪 Running Tests

```bash
pip install tv-scraper-py[dev]
pytest tests/ -v
```

---

## 🤝 Contributing

1. Fork the repository
2. Create feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m "Add feature"`
4. Push: `git push origin feature/amazing-feature`
5. Open a Pull Request

---

## ⚠️ Disclaimer

This library is for **educational and research purposes only**. Respect TradingView's Terms of Service.

---

## 📄 License

MIT License — See [LICENSE](LICENSE)

---

## 📬 Contact

- **Author:** Anurag Jha
- **Email:** anuragjha507@gmail.com
- **GitHub:** [@anuragjha0001](https://github.com/anuragjha0001)
- **PyPI:** [tv-scraper-py](https://pypi.org/project/tv-scraper-py/)
- **Issues:** [Report Bug](https://github.com/anuragjha0001/tv-scraper-py/issues)

---

**Made with ❤️ by Anurag Jha**
