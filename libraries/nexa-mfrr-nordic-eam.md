---
label: nexa-mfrr-nordic-eam
icon: broadcast
order: 80
description: Python library for building, validating, and serialising mFRR energy activation market bids for Nordic TSOs.
---

# nexa-mfrr-nordic-eam

Python library for building, validating, and serialising mFRR energy activation market bids for the Nordic TSOs (Statnett, Svenska kraftnät, Energinet, Fingrid). Handles the full bid lifecycle from construction to CIM XML serialisation for BSPs who build their own systems.

[!badge variant="info" text="Python 3.11+"] [!badge variant="light" text="MIT License"] [!badge variant="warning" text="Pre-release 0.4.0b1"]

```python
pip install nexa-mfrr-nordic-eam
```

[GitHub](https://github.com/phasenexa/nexa-mfrr-nordic-eam) | [PyPI](https://pypi.org/project/nexa-mfrr-nordic-eam/)

!!! warning Work in progress
The API, documentation, and feature set are under active development and subject to change.
!!!

---

## Why this exists

Submitting mFRR bids to Nordic TSOs requires producing CIM XML documents that conform to the NBM ReserveBid schema. Every BSP building their own trading system ends up writing the same serialisation code, the same gate closure calculations, and the same TSO-specific validation logic.

nexa-mfrr-nordic-eam handles that infrastructure so you can focus on strategy:

- **Correct domain model** for ENTSO-E mFRR bid types: simple, linked, exclusive groups, multipart, inclusive groups
- **TSO-specific validation** before you send, not after the TSO rejects you
- **CIM XML serialisation** with correct XSD element ordering and namespace handling
- **Gate closure calculations** with DST awareness and pre/post-MARI timing
- **Fluent builder API** that makes bid construction readable and type-safe

---

## Quick start

```python
from datetime import datetime, timezone
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

---

## Supported bid types

### Simple bids

Divisible and indivisible bids with full attribute support:

```python
# Divisible down-regulation bid with minimum volume
bid = (
    Bid.down(volume_mw=30, price_eur=40.00)
    .divisible(min_volume_mw=5)
    .for_mtu("2026-03-21T10:00Z")
    .resource("NOKG90901", coding_scheme="NNO")
    .build()
)
```

### Technically linked bids

Link bids across MTUs to prevent double-activation:

```python
from nexa_mfrr_eam import TechnicalLink, Direction, MarketProductType, BiddingZone

link = (
    TechnicalLink(bidding_zone=BiddingZone.SE3)
    .resource("REG-OBJ-SE-001", coding_scheme="A01")
    .add_mtu(
        mtu="2026-03-21T10:00Z",
        direction=Direction.UP,
        volume_mw=25,
        price_eur=90.00,
        product_type=MarketProductType.SCHEDULED_AND_DIRECT,
    )
    .add_mtu("2026-03-21T10:15Z", Direction.UP, 25, 90.00)
    .add_mtu("2026-03-21T10:30Z", Direction.UP, 25, 90.00)
    .build()
)
```

### Complex bid groups

Exclusive, multipart, and inclusive groups for more complex strategies:

```python
from nexa_mfrr_eam import MultipartGroup, BiddingZone

group = (
    MultipartGroup(bidding_zone=BiddingZone.NO2)
    .direction(Direction.UP)
    .for_mtu("2026-03-21T10:00Z")
    .resource("NOKG90901", coding_scheme="NNO")
    .add_component(volume_mw=20, price_eur=50.00)
    .add_component(volume_mw=15, price_eur=75.00)
    .add_component(volume_mw=10, price_eur=120.00)
    .build()
)
```

### Conditional linking

Three condition codes supported (A55, A56, A67):

```python
from nexa_mfrr_eam import ConditionalStatus

bid_qh_minus_1 = (
    Bid.up(volume_mw=30, price_eur=70.00)
    .divisible(min_volume_mw=5)
    .for_mtu("2026-03-21T10:15Z")
    .resource("NOKG90901", coding_scheme="NNO")
    .build()
)

bid_qh_0 = (
    Bid.up(volume_mw=30, price_eur=70.00)
    .divisible(min_volume_mw=5)
    .for_mtu("2026-03-21T10:30Z")
    .resource("NOKG90901", coding_scheme="NNO")
    .conditionally_available()
    .link_to(bid_qh_minus_1, status=ConditionalStatus.NOT_AVAILABLE_IF_ACTIVATED)
    .build()
)
```

---

## Timing utilities

Gate closure calculations with full DST handling:

```python
from nexa_mfrr_eam.timing import gate_closure, MARIMode, current_mtu, mtu_range
from datetime import datetime, timezone

mtu_start = datetime(2026, 3, 21, 10, 0, tzinfo=timezone.utc)

gc = gate_closure(mtu_start, mari_mode=MARIMode.PRE_MARI)
print(f"BSP GCT (BEGCT): {gc.bsp_gct}")     # 09:15:00 UTC (QH-45)
print(f"TSO GCT:         {gc.tso_gct}")      # 09:45:00 UTC (QH-15)
print(f"Activation at:   {gc.activation}")   # 09:52:30 UTC (QH-7.5)
print(f"Gate open now?   {gc.is_gate_open()}")

# 96 MTUs in a normal day, 92 on the spring DST transition
mtus = mtu_range("2026-03-21T00:00Z", "2026-03-22T00:00Z")
print(f"MTUs: {len(mtus)}")
```

---

## Pandas integration

Build bid portfolios directly from a DataFrame (optional dependency):

```python
pip install nexa-mfrr-nordic-eam[pandas]
```

```python
import pandas as pd
from nexa_mfrr_eam.pandas import bids_from_dataframe
from nexa_mfrr_eam import BidDocument, TSO, BiddingZone, MarketProductType

df = pd.DataFrame({
    "mtu_start": pd.to_datetime(["2026-03-21T10:00Z", "2026-03-21T10:15Z", "2026-03-21T10:30Z"]),
    "direction": ["up", "up", "up"],
    "volume_mw": [50, 45, 55],
    "price_eur": [72.30, 74.10, 69.50],
    "min_volume": [10, 10, 10],
    "resource":   ["NOKG90901"] * 3,
})

bids = bids_from_dataframe(
    df,
    bidding_zone=BiddingZone.NO2,
    resource_coding_scheme="NNO",
    product_type=MarketProductType.SCHEDULED_AND_DIRECT,
    technical_link=True,
)

doc = (
    BidDocument(tso=TSO.STATNETT)
    .sender(party_id="9999909919920", coding_scheme="A10")
    .add_bids(bids)
    .build()
)
```

---

## TSO support

| TSO | Country | Min volume | Exclusive groups | Multipart | Technical linking | Conditional linking |
|-----|---------|-----------|:---:|:---:|:---:|:---:|
| Statnett | NO | 10 MW | Yes | Yes | Yes | Yes |
| Svenska kraftnät | SE | 1 MW | Yes | Yes | Yes | Yes |
| Energinet | DK | 1 MW | Yes | Yes | Yes | Yes |
| Fingrid | FI | 1 MW | Planned | Planned | Planned | Planned |

Bidding zones: NO1-NO5, SE1-SE4, DK1-DK2, FI.

---

## What it does not do (yet)

The library serialises bids to CIM XML and validates them. It does not manage the transport layer. You are responsible for:

- ECP/EDX endpoint deployment and connectivity
- Parsing TSO acknowledgement responses (planned)
- Parsing activation and availability documents (planned)
- Heartbeat responder (planned)

---

## API reference

**Core builders**

| Class | Description |
|-------|-------------|
| `Bid` | Simple bid builder (fluent API) |
| `ExclusiveGroup` | Exclusive group builder |
| `MultipartGroup` | Multipart bid builder |
| `InclusiveGroup` | Inclusive group builder |
| `TechnicalLink` | Cross-MTU technical linking builder |
| `BidDocument` | Document builder |
| `BuiltBidDocument` | Built document with `.validate()` and `.to_xml()` |

**Key enums**

| Enum | Values |
|------|--------|
| `Direction` | `UP`, `DOWN` |
| `MarketProductType` | `SCHEDULED_ONLY`, `SCHEDULED_AND_DIRECT`, `PERIOD_SHIFT_ONLY`, ... |
| `BiddingZone` | `NO1`-`NO5`, `SE1`-`SE4`, `DK1`-`DK2`, `FI` |
| `TSO` | `STATNETT`, `FINGRID`, `ENERGINET`, `SVENSKA_KRAFTNAT` |
| `MARIMode` | `PRE_MARI`, `POST_MARI` |
| `ConditionalStatus` | `NOT_AVAILABLE_IF_ACTIVATED`, `AVAILABLE_IF_ACTIVATED`, `AVAILABLE_IF_NOT_ACTIVATED` |
