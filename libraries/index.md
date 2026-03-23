---
label: Libraries
icon: package
order: 80
expanded: true
description: Open source libraries for European energy trading infrastructure.
---

# Libraries

Phase Nexa is a collection of focused, composable libraries. Each one solves a specific problem. Use them together or independently.

All libraries are MIT-licensed.

---

| Library | Language | What it does | Install |
|---------|----------|--------------|---------|
| [nexa-marketdata](/libraries/nexa-marketdata/) | Python | Unified market data client for Nord Pool, EPEX SPOT, ENTSO-E, EEX | `pip install nexa-marketdata` |
| [nexa-bidkit](/libraries/nexa-bidkit/) | Python | Day-ahead and intraday auction bid generation | `pip install nexa-bidkit` |
| [nexa-connect](/libraries/nexa-connect/) | Go | Exchange connectivity SDK (FIX, REST, WebSocket) | `go get github.com/phasenexa/nexa-connect` |
| [nexa-mcp](/libraries/nexa-mcp/) | Python | MCP server for LLM-powered trading workflows | `pip install nexa-mcp` |

---

## How they fit together

```
                     Your Trading System
                            |
              +-------------+-------------+
              |             |             |
        nexa-marketdata  nexa-bidkit  nexa-connect
              |             |             |
              +------+------+      +------+
                     |             |
              Nord Pool / EPEX SPOT / EEX / ENTSO-E
```

**nexa-marketdata** fetches the data. **nexa-bidkit** builds the bids. **nexa-connect** submits them. Each library works independently, but they are designed to compose naturally.

**nexa-mcp** sits alongside all of them, exposing their capabilities to LLM clients for interactive querying and exploration.

---

## Planned libraries

These are on the roadmap. Development priority is driven by community demand.

| Library | Language | What it will do | Status |
|---------|----------|-----------------|--------|
| nexa-forecast | Python | Short-term price and load forecasting | Planned |
| nexa-position | Go | Real-time position management | Planned |
| nexa-backtest | Python | Energy market backtesting framework | Planned |
| nexa-scheduler | Go | Nomination and scheduling engine | Planned |
| nexa-risk | Python | Lightweight risk analytics (VaR, CVaR) | Planned |
| nexa-signals | Go | Market signal detection and alerting | Planned |
| nexa-excel | TS/C# | Excel add-in for market data and bidding | Planned |

!!! info
Want to influence the roadmap? [Open an issue](https://github.com/phasenexa/phasenexa.github.io/issues) or [join the community](/community/).
!!!
