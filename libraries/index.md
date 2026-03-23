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
| [nexa-mfrr-nordic-eam](/libraries/nexa-mfrr-nordic-eam/) | Python | mFRR energy activation market bids for Nordic TSOs | `pip install nexa-mfrr-nordic-eam` |

---

## How they fit together

```
                     Your Trading System
                            |
         +------------------+------------------+
         |                  |                  |
   nexa-marketdata     nexa-bidkit    nexa-mfrr-nordic-eam
         |                  |                  |
         +--------+---------+          +-------+
                  |                    |
         Nord Pool / EPEX SPOT / EEX   Nordic TSOs
              / ENTSO-E                (Statnett, SVK,
                                        Energinet, Fingrid)
```

**nexa-marketdata** fetches the data. **nexa-bidkit** builds day-ahead and intraday bids. **nexa-mfrr-nordic-eam** constructs and serialises mFRR balancing bids for Nordic TSOs. Each library works independently, but they are designed to compose naturally.

---

## Planned libraries

These are on the roadmap. Development priority is driven by community demand.

| Library | Language | What it will do | Status |
|---------|----------|-----------------|--------|
| nexa-connect | Go | Exchange connectivity SDK (FIX, REST, WebSocket) | Coming soon |
| nexa-mcp | Python | MCP server for LLM-powered trading workflows | Coming soon |
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
