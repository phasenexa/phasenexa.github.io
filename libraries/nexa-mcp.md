---
label: nexa-mcp
icon: cpu
order: 70
description: MCP server exposing Phase Nexa tools to LLM clients.
---

# nexa-mcp

MCP (Model Context Protocol) server that exposes Phase Nexa data and tools to LLM clients. Query market data, generate bids, and explore trading positions from Claude, ChatGPT, or any MCP-compatible client.

[!badge variant="info" text="Python 3.10+"] [!badge variant="light" text="MIT License"] [!badge variant="warning" text="In Development"]

[GitHub](https://github.com/phasenexa/nexa-mcp)

---

## Why this exists

Trading desks are not just developers. Analysts, portfolio managers, and traders need access to the same data and tools without writing Python scripts. nexa-mcp bridges that gap by making Phase Nexa capabilities available through natural language.

Ask your LLM: "What were the day-ahead prices for NO1 yesterday?" or "Build me a block bid for 50 MW between 08:00 and 16:00 at 48 EUR/MWh" and get real answers backed by real data and real bid generation logic.

---

## How it works

nexa-mcp implements the [Model Context Protocol](https://modelcontextprotocol.io/) standard, exposing Phase Nexa libraries as tools that LLM clients can call.

```
LLM Client (Claude, ChatGPT, etc.)
        |
        | MCP Protocol
        |
   nexa-mcp server
        |
   +----+----+----+
   |         |         |
nexa-marketdata  nexa-bidkit  nexa-connect
```

### Available tools

| Tool | Description |
|------|-------------|
| `get_day_ahead_prices` | Fetch day-ahead prices for any supported bidding zone |
| `get_generation_data` | Fetch generation by fuel type |
| `get_load_data` | Fetch actual and forecast load |
| `build_block_bid` | Generate a block bid with validation |
| `build_portfolio` | Assemble and validate a bid portfolio |
| `list_bidding_zones` | List supported bidding zones and their metadata |

---

## Quick start

```bash
# Install the MCP server
pip install nexa-mcp

# Run the server
nexa-mcp serve --port 8080
```

Then configure your MCP client to connect to `http://localhost:8080/mcp`.

For Claude Desktop, add to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "phase-nexa": {
      "command": "nexa-mcp",
      "args": ["serve"],
      "env": {
        "ENTSOE_API_KEY": "your-api-key",
        "NORDPOOL_API_KEY": "your-api-key"
      }
    }
  }
}
```

---

## Who is this for

- **Traders** who want market data without leaving their chat interface
- **Analysts** who want to explore data through natural language queries
- **Portfolio managers** who want quick bid scenario generation
- **Anyone** on a trading desk who does not write Python but needs the data
