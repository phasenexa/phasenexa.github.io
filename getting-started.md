---
label: Get Started
icon: rocket
order: 90
description: Get up and running with Phase Nexa in minutes.
---

# Getting Started

Phase Nexa libraries are distributed as standard packages. Install what you need, ignore the rest. No monolithic framework, no vendor lock-in.

---

## Pick your starting point

+++ Market Data

**nexa-marketdata** is the best entry point if you need price data, generation data, or load data from European exchanges.

```python
pip install nexa-marketdata
```

```python
from nexa_marketdata import NordPool

client = NordPool()

# Fetch day-ahead prices for NO1 (South Norway)
prices = client.day_ahead_prices(
    bidding_zone="NO1",
    date="2025-10-01"
)

for interval in prices:
    print(f"{interval.start} -> {interval.price} EUR/MWh")
```

+++ Bid Generation

**nexa-bidkit** is the starting point if you need to build and submit auction bids.

```python
pip install nexa-bidkit
```

```python
from nexa_bidkit import Portfolio, BlockBid
from nexa_bidkit.nordpool import NordPoolSerializer

# Build a simple block bid
bid = BlockBid(
    name="Wind_Block_01",
    periods=range(8, 20),  # Hours 08:00-20:00
    price=45.50,           # EUR/MWh
    volume=25.0,           # MW
    min_acceptance_ratio=1.0
)

# Serialise for Nord Pool submission
serializer = NordPoolSerializer()
payload = serializer.serialize(Portfolio(bids=[bid]))
```

+++ mFRR Bids

**nexa-mfrr-nordic-eam** is the starting point for BSPs building Nordic mFRR energy activation market bid workflows.

```python
pip install nexa-mfrr-nordic-eam
```

```python
from nexa_mfrr_eam import (
    Bid, BidDocument, Direction, MarketProductType,
    BiddingZone, TSO, MARIMode,
)

# Create a simple divisible up-regulation bid
bid = (
    Bid.up(volume_mw=50, price_eur=85.50)
    .divisible(min_volume_mw=10)
    .for_mtu("2026-03-21T10:00Z")
    .resource("NOKG90901", coding_scheme="NNO")
    .product_type(MarketProductType.SCHEDULED_AND_DIRECT)
    .build()
)

# Wrap in a document targeting Statnett
doc = (
    BidDocument(tso=TSO.STATNETT)
    .sender(party_id="9999909919920", coding_scheme="A10")
    .add_bid(bid)
    .build()
)

# Validate, then serialise to CIM XML
errors = doc.validate(mari_mode=MARIMode.PRE_MARI)
if not errors:
    xml_bytes = doc.to_xml()
    # Send xml_bytes via your ECP/EDX endpoint
```

+++ Exchange Connectivity

**nexa-connect** is for teams building or maintaining direct exchange connections.

```bash
go get github.com/phasenexa/nexa-connect
```

```go
package main

import (
    "context"
    "log"

    "github.com/phasenexa/nexa-connect/nordpool"
)

func main() {
    client, err := nordpool.NewClient(nordpool.Config{
        APIKey:  "your-api-key",
        BaseURL: "https://api.nordpoolgroup.com/v2",
    })
    if err != nil {
        log.Fatal(err)
    }

    ctx := context.Background()
    markets, err := client.ListMarkets(ctx)
    if err != nil {
        log.Fatal(err)
    }

    for _, m := range markets {
        log.Printf("Market: %s (%s)", m.Name, m.ID)
    }
}
```

+++

---

## Requirements

| Library         | Language   | Minimum Version |
|-----------------|------------|-----------------|
| nexa-marketdata | Python     | 3.10+           |
| nexa-bidkit     | Python     | 3.10+           |
| nexa-connect    | Go         | 1.21+           |
| nexa-mfrr-nordic-eam | Python | 3.11+           |
| nexa-mcp        | Python     | 3.10+           |

---

## What you will need

Most Phase Nexa libraries interact with exchange APIs. You will typically need:

- **API credentials** from your exchange (Nord Pool, EPEX SPOT, etc.)
- **ENTSO-E API key** from the [ENTSO-E Transparency Platform](https://transparency.entsoe.eu/) (free registration)
- A working **Python 3.10+** or **Go 1.21+** environment

!!! info
Phase Nexa libraries handle authentication, rate limiting, and retry logic. You provide the credentials, we handle the plumbing.
!!!

---

## Next steps

- Browse the [library documentation](/libraries/) for detailed API references
- Check the [GitHub organisation](https://github.com/phasenexa) for source code and issue trackers
- Read about [premium support and hosted services](/premium/) coming soon
- [Join the community](/community/) and let us know what you are building
