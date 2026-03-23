---
label: nexa-marketdata
icon: database
order: 100
description: Unified Python client for European power market data. Nord Pool, EPEX SPOT, ENTSO-E, and EEX.
---

# nexa-marketdata

Unified API client for European power market data sources. One interface for Nord Pool, EPEX SPOT, ENTSO-E Transparency Platform, and EEX.

[!badge variant="info" text="Python 3.10+"] [!badge variant="light" text="MIT License"] [!badge variant="warning" text="In Development"]

```python
pip install nexa-marketdata
```

[GitHub](https://github.com/phasenexa/nexa-marketdata) | [PyPI](https://pypi.org/project/nexa-marketdata/)

---

## Why this exists

The European power market data landscape is fragmented. Every exchange has its own API, its own data format, its own quirks. The community alternatives (entsoe-py, kipe/nordpool) are volunteer-maintained and frequently break when APIs change.

nexa-marketdata provides a single, well-tested client that handles:

- **Multiple exchanges** through a consistent interface
- **15-minute MTU data** natively (not bolted on as an afterthought)
- **Rate limiting** so you do not get banned from ENTSO-E at 03:00
- **Timezone normalisation** across bidding zones
- **Caching** to avoid redundant API calls
- **Proper error handling** with meaningful exceptions, not silent failures

---

## Quick start

```python
from nexa_marketdata import EntsoE

# Initialise with your ENTSO-E API key
client = EntsoE(api_key="your-api-key")

# Fetch day-ahead prices for Germany-Luxembourg
prices = client.day_ahead_prices(
    bidding_zone="DE-LU",
    start="2025-10-01",
    end="2025-10-02"
)

# Returns a list of MarketData objects with 15-minute resolution
for p in prices:
    print(f"{p.start} {p.price:>8.2f} EUR/MWh")
```

---

## Supported data sources

| Exchange | Day-Ahead Prices | Intraday Prices | Generation | Load | Cross-Border Flows |
|----------|:---:|:---:|:---:|:---:|:---:|
| ENTSO-E  | Yes | Yes | Yes | Yes | Yes |
| Nord Pool | Yes | Yes | - | - | - |
| EPEX SPOT | Yes | Yes | - | - | - |
| EEX | Yes | - | - | - | - |

---

## Key features

### Consistent data model

All data is returned as `MarketData` objects regardless of source. No more parsing different JSON structures from different exchanges.

```python
@dataclass
class MarketData:
    start: datetime       # Interval start (UTC)
    end: datetime         # Interval end (UTC)
    value: float          # Price or volume
    unit: str             # EUR/MWh, MW, etc.
    bidding_zone: str     # e.g. "NO1", "DE-LU", "FR"
    resolution: Resolution # FIFTEEN_MIN, HOURLY, etc.
    source: DataSource    # ENTSO_E, NORD_POOL, etc.
```

### Automatic resolution handling

The library handles the hourly-to-15-minute transition transparently. Request data for a period that spans the September 2025 MTU change, and you get correctly aligned intervals.

### Built-in caching

```python
from nexa_marketdata import EntsoE, FileCache

client = EntsoE(
    api_key="your-api-key",
    cache=FileCache(path="./cache", ttl=3600)
)
```

---

## Premium: Hosted Data Proxy

[!badge variant="warning" text="Coming Soon"]

For teams that need higher reliability, historical backfill, and no rate limit headaches:

- Cached proxy with sub-second response times
- Historical data backfill API (2015 onwards)
- 99.9% uptime SLA
- Priority support

Pricing starts at EUR 49/month per seat. [Learn more](/premium/).
