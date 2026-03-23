---
label: Home
icon: home
order: 100
description: Developer tools for European energy trading. Open source libraries for market data, bid generation, and exchange connectivity.
---

# Phase Nexa

## Developer tools for the 75% who build their own.

75% of Nord Pool's customer base connects directly via API. They are quants, data scientists, and developers who build and maintain their own trading infrastructure. Phase Nexa gives them better tools to do it.

---

## The problem

Every energy trading company in Europe has developers writing the same code: ENTSO-E API wrappers that break when the format changes. Bid generators that need rebuilding for 15-minute MTUs. Position trackers held together with spreadsheets and prayer.

The ISVs want to sell you a platform. We want to give you libraries.

---

## The libraries

:::content
[!badge variant="info" text="Python"]

### nexa-marketdata

Unified API client for Nord Pool, EPEX SPOT, ENTSO-E, and EEX. Handles 15-minute MTU data, rate limiting, caching, and timezone normalisation. Replaces entsoe-py and the fragmented community wrappers.

```python
pip install nexa-marketdata
```

[Learn more](/libraries/nexa-marketdata/) | [GitHub](https://github.com/phasenexa/nexa-marketdata)
:::

:::content
[!badge variant="info" text="Python"]

### nexa-bidkit

Day-ahead and intraday auction bid generation. Correct domain modelling of EUPHEMIA bid types (block bids, linked orders, exclusive groups) with exchange-specific serialisation for Nord Pool, EPEX SPOT, and EEX.

```python
pip install nexa-bidkit
```

[Learn more](/libraries/nexa-bidkit/) | [GitHub](https://github.com/phasenexa/nexa-bidkit)
:::

:::content
[!badge variant="success" text="Go"]

### nexa-connect

Exchange connectivity SDK. FIX protocol, REST/WebSocket connections to Nord Pool, EPEX SPOT, EEX. Session management, heartbeat, reconnection, order lifecycle.

```bash
go get github.com/phasenexa/nexa-connect
```

[Learn more](/libraries/nexa-connect/) | [GitHub](https://github.com/phasenexa/nexa-connect)
:::

:::content
[!badge variant="info" text="Python"]

### nexa-mcp

MCP server exposing Phase Nexa data and tools to LLM clients. Query market data, generate bids, and monitor positions from Claude, ChatGPT, or any MCP-compatible client.

[Learn more](/libraries/nexa-mcp/) | [GitHub](https://github.com/phasenexa/nexa-mcp)
:::

---

## Why now

On 30 September 2025, European power markets transitioned from hourly to 15-minute Market Time Units. Instead of 24 intervals per day, traders now manage 96. Every bidding algorithm, data pipeline, and position tracker needs updating.

For self-builders, this is not a software update you click a button for. You have to rebuild. Phase Nexa gives you the building blocks to do it properly.

---

## Open source, commercially supported

Every Phase Nexa library is MIT-licensed. Use them freely. Fork them. Build on them. Contribute to them.

Premium support, hosted services, and SLAs are coming soon for teams that need them.

[!badge variant="warning" text="Coming Soon"] **Premium tiers** including hosted data proxies, bid optimisation services, and priority support. [Learn more](/premium/).

---

## Built by energy trading developers

Phase Nexa is built by people who have spent years building trading systems. We know what breaks at 06:00 when the day-ahead auction clears. We know what ENTSO-E does to your pipeline when they change the API without notice. We know because we have been there.

[Get started](/getting-started/) | [View on GitHub](https://github.com/phasenexa) | [Join the community](/community/)
