---
label: nexa-bidkit
icon: tools
order: 90
description: Day-ahead and intraday auction bid generation for European power markets.
---

# nexa-bidkit

Day-ahead and intraday auction bid generation library for European power markets. Correct domain modelling of EUPHEMIA bid types with exchange-specific serialisation.

[!badge variant="info" text="Python 3.10+"] [!badge variant="light" text="MIT License"] [!badge variant="warning" text="In Development"]

```python
pip install nexa-bidkit
```

[GitHub](https://github.com/phasenexa/nexa-bidkit) | [PyPI](https://pypi.org/project/nexa-bidkit/)

---

## Why this exists

Building bids for European power auctions is complex. The EUPHEMIA algorithm supports block bids, linked orders, exclusive groups, flexible orders, and merit orders, each with specific constraints that vary by exchange. Getting the domain model wrong means rejected bids or, worse, unintended market exposure.

nexa-bidkit provides:

- **Correct EUPHEMIA bid type modelling** (block bids, linked orders, exclusive groups, flexible orders, merit orders)
- **Exchange-specific serialisation** for Nord Pool, EPEX SPOT, and EEX
- **Pre-submission validation** catching constraint violations before they reach the exchange
- **15-minute MTU support** natively across all bid types
- **Graph plotting** for curved orders and portfolio visualisation

---

## Quick start

```python
from nexa_bidkit import Portfolio, BlockBid, LinkedBid
from nexa_bidkit.nordpool import NordPoolSerializer

# Simple block bid: sell 25 MW from 08:00-20:00 at 45.50 EUR/MWh
block = BlockBid(
    name="Wind_Block_01",
    periods=range(8, 20),
    price=45.50,
    volume=25.0,
    min_acceptance_ratio=1.0,
)

# Linked bid: parent-child relationship
parent = BlockBid(
    name="CCGT_Startup",
    periods=range(6, 8),
    price=35.00,
    volume=50.0,
)

child = LinkedBid(
    name="CCGT_Running",
    periods=range(8, 22),
    price=52.00,
    volume=200.0,
    parent=parent,
)

# Assemble and serialise
portfolio = Portfolio(bids=[block, parent, child])
serializer = NordPoolSerializer()

# Validate before submission
errors = serializer.validate(portfolio)
if errors:
    for e in errors:
        print(f"Validation error: {e}")
else:
    payload = serializer.serialize(portfolio)
```

---

## Supported bid types

| Bid Type | Description | Nord Pool | EPEX SPOT | EEX |
|----------|-------------|:---------:|:---------:|:---:|
| Hourly/MTU Orders | Simple price-quantity pairs per interval | Yes | Yes | Yes |
| Block Bids | All-or-nothing across multiple intervals | Yes | Yes | Yes |
| Linked Orders | Parent-child dependencies | Yes | Yes | - |
| Exclusive Groups | Mutually exclusive bid sets | Yes | Yes | - |
| Flexible Orders | Price-only, exchange assigns interval | Yes | - | - |
| Merit Orders | Stepped supply/demand curves | Yes | Yes | Yes |

---

## Key concepts

### EUPHEMIA is a clearing algorithm, not a wire protocol

No exchange accepts EUPHEMIA as a submission format. Each exchange has its own API, its own payload structure, and its own validation rules. nexa-bidkit models the EUPHEMIA bid types correctly at the domain level, then handles the exchange-specific translation for you.

### Validation catches mistakes early

Every bid type has constraints: minimum acceptance ratios, maximum block sizes, period alignment rules. The validator checks these against exchange-specific rules before you attempt submission.

```python
errors = serializer.validate(portfolio)
# -> ["Block 'Wind_Block_01': min_acceptance_ratio must be between 0.0 and 1.0"]
```

---

## Premium: Hosted Bid Optimisation

[!badge variant="warning" text="Coming Soon"]

For teams that want optimal bid portfolio construction:

- MILP optimisation engine given asset constraints and price forecasts
- Cloud-hosted solver with sub-minute solve times
- Excel add-in for non-coding desk users

Pricing starts at EUR 99/month. [Learn more](/premium/).
