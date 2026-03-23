---
label: nexa-connect
icon: plug
order: 80
description: Exchange connectivity SDK for European power markets. FIX, REST, and WebSocket.
---

# nexa-connect

Exchange connectivity SDK for European power markets. Handles FIX protocol, REST, and WebSocket connections to Nord Pool, EPEX SPOT, and EEX.

[!badge variant="success" text="Go 1.21+"] [!badge variant="light" text="MIT License"] [!badge variant="warning" text="In Development"]

```bash
go get github.com/phasenexa/nexa-connect
```

[GitHub](https://github.com/phasenexa/nexa-connect)

---

## Why this exists

Every energy trading team building direct exchange connections writes the same boilerplate: session management, heartbeat handling, reconnection logic, order lifecycle tracking, FIX message parsing. It is unglamorous, error-prone work that has nothing to do with trading strategy.

nexa-connect provides:

- **Multi-exchange support** through clean, consistent Go interfaces
- **Session management** with automatic reconnection and heartbeat
- **Order lifecycle** tracking from submission through fill or rejection
- **FIX protocol** implementation for exchanges that require it
- **REST and WebSocket** clients for modern exchange APIs
- **Structured logging** and observability hooks

---

## Quick start

```go
package main

import (
    "context"
    "log/slog"
    "os"

    "github.com/phasenexa/nexa-connect/nordpool"
)

func main() {
    logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))

    client, err := nordpool.NewClient(nordpool.Config{
        APIKey:  os.Getenv("NORDPOOL_API_KEY"),
        BaseURL: "https://api.nordpoolgroup.com/v2",
        Logger:  logger,
    })
    if err != nil {
        logger.Error("failed to create client", "error", err)
        os.Exit(1)
    }
    defer client.Close()

    ctx := context.Background()

    // List available markets
    markets, err := client.ListMarkets(ctx)
    if err != nil {
        logger.Error("failed to list markets", "error", err)
        os.Exit(1)
    }

    for _, m := range markets {
        logger.Info("market", "name", m.Name, "id", m.ID, "status", m.Status)
    }
}
```

---

## Design principles

nexa-connect follows Go conventions inspired by HashiCorp's codebases (Terraform, HCL):

- **Clean interfaces** with small surface area
- **Minimal dependencies** outside the standard library
- **Explicit error handling** with wrapped, contextual errors
- **Functional options** for configuration
- **Context propagation** for cancellation and timeouts
- **Structured logging** via `log/slog`

---

## Supported exchanges

| Exchange | Protocol | Status |
|----------|----------|--------|
| Nord Pool v2 API | REST | In Development |
| EPEX SPOT | REST | Planned |
| EEX | FIX 4.4 | Planned |
| Euronext Optiq | FIX 5.0 | Planned |

---

## Premium: Managed Gateway

[!badge variant="warning" text="Coming Soon"]

For teams that want zero-ops exchange connectivity:

- Managed gateway service with redundancy and monitoring
- Automatic failover and reconnection
- Order audit trail and compliance logging
- 99.9% uptime SLA

Pricing starts at EUR 199/month. [Learn more](/premium/).
