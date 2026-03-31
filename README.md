# Orbuc MCP Server

Remote MCP server providing AI agents with direct access to independent on-chain stablecoin market cap and Bitcoin institutional holdings data.

**Endpoint:** `https://mcp.orbuc.io/mcp/orbuc`

**Protocol:** MCP Streamable HTTP (JSON-RPC 2.0 over POST)
**Authentication:** None required (open access)
**Transport:** Stateless HTTP — no session persistence

---

## What is Orbuc?

Orbuc is a digital asset research and investment firm. Orbuc produces its own data, tracking two core datasets independently — no third-party aggregators:

- **Stablecoin Market Cap** — direct on-chain `totalSupply()` RPC calls across 19 blockchain networks covering 13 stablecoins (USDT, USDC, USDS, DAI, USDe, FDUSD, PYUSD, USD1, BUIDL, BUIDL-I, crvUSD, GHO, FRAX). Updated daily at 03:00 UTC.

- **Bitcoin Institutional Holdings** — compiled from SEC EDGAR 8-K filings, on-chain Etherscan data, and verified public disclosures across 6 segments (ETF funds, public companies, governments, private companies, DeFi/wrapped BTC, exchanges & custodians). Updated daily at 02:00 UTC.

---

## Available Tools (12)

### Stablecoin Market Cap

| Tool | Description | Parameters |
|------|-------------|------------|
| `orbuc_stablecoin_mcap` | Historical daily total stablecoin market cap | `days`, `start_date`, `end_date` |
| `orbuc_stablecoin_latest` | Latest snapshot — all 13 coins with per-chain breakdown | — |
| `orbuc_stablecoin_coin` | Historical supply for a specific stablecoin | `symbol`, `days`, `start_date`, `end_date` |
| `orbuc_stablecoin_coin_latest` | Latest supply for one coin with chain breakdown | `symbol` |
| `orbuc_stablecoin_aggregate` | Daily aggregate by total, symbol, or chain | `level`, `start_date`, `end_date` |
| `orbuc_stablecoin_chains` | Supply distribution across chains | `days` |

### Bitcoin Institutional Holdings

| Tool | Description | Parameters |
|------|-------------|------------|
| `orbuc_btc_holdings_current` | Latest BTC held by institutions per segment | — |
| `orbuc_btc_holdings_segments` | Segment breakdown with confidence and sources | — |
| `orbuc_btc_holdings_daily` | Daily time series, optionally by segment | `segment`, `start_date`, `end_date` |
| `orbuc_btc_holdings_weekly` | Weekly aggregated holdings | `segment` |
| `orbuc_btc_health` | BTC tracker health status | — |
| `orbuc_stablecoin_health` | Stablecoin tracker health status | — |

---

## Quick Start

### Claude.ai / Claude Desktop

Add as a custom MCP connector:
```
https://mcp.orbuc.io/mcp/orbuc
```

### VS Code / Cursor / Windsurf

Add to `.vscode/mcp.json`:
```json
{
  "servers": {
    "orbuc": {
      "type": "http",
      "url": "https://mcp.orbuc.io/mcp/orbuc"
    }
  }
}
```

### Claude Desktop Config

Add to `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "orbuc": {
      "type": "url",
      "url": "https://mcp.orbuc.io/mcp/orbuc"
    }
  }
}
```

### Python (MCP SDK)

```python
from mcp import ClientSession
from mcp.client.streamable_http import streamablehttp_client

async def query_orbuc():
    async with streamablehttp_client("https://mcp.orbuc.io/mcp/orbuc") as (read, write, _):
        async with ClientSession(read, write) as session:
            await session.initialize()
            tools = await session.list_tools()
            print(f"{len(tools.tools)} tools available")

            result = await session.call_tool("orbuc_btc_holdings_current", {})
            print(result.content[0].text)
```

### cURL

```bash
# Initialize
curl -X POST https://mcp.orbuc.io/mcp/orbuc \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'

# List tools
curl -X POST https://mcp.orbuc.io/mcp/orbuc \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}'

# Call a tool
curl -X POST https://mcp.orbuc.io/mcp/orbuc \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"orbuc_stablecoin_mcap","arguments":{"days":7}}}'
```

---

## Example Queries

Once connected, ask your AI agent:

- *"What's the total stablecoin market cap today?"*
- *"Show me USDT supply broken down by chain"*
- *"How much BTC do public companies hold vs ETF funds?"*
- *"What's the daily change in ETF segment BTC holdings over the past two weeks?"*
- *"Compare USDC and USDT market share across Ethereum, Tron, and Solana"*
- *"Which BTC segments have high confidence data vs carry-forward estimates?"*

---

## Data Coverage

### Stablecoins
- **Coins:** USDT, USDC, USDS, DAI, USDe, FDUSD, PYUSD, USD1, BUIDL, BUIDL-I, crvUSD, GHO, FRAX
- **Chains:** Ethereum, Tron, Solana, Arbitrum, Base, BSC, Polygon, Avalanche, Optimism, TON, Near, Stellar, Sui, Celo, Gnosis, Linea, Mantle, zkSync, Algorand
- **History from:** August 2024
- **Source:** Direct on-chain `totalSupply()` RPC calls

### Bitcoin Holdings
- **Segments:** public_companies, etf_funds, governments, private_companies, defi_other, exchanges_custodians
- **History from:** August 2025
- **Sources:** SEC EDGAR, Etherscan, bitbo.io, GNews event detection

---

## x402 Payment Protocol

Orbuc also supports [x402](https://x402.org) micropayments for autonomous agent access via USDC on Base. The MCP server is currently **free and open access** — x402 pricing may be introduced in future.

Discovery file: `https://orbuc.io/.well-known/x402.json`

---

## Links

- **Website:** [orbuc.io](https://orbuc.io)
- **MCP Endpoint:** `https://mcp.orbuc.io/mcp/orbuc`
- **Health Check:** `https://mcp.orbuc.io/mcp/health`
- **Contact:** info@orbuc.io

---

## License

Proprietary. Data provided by Orbuc Research. See [data disclaimer](https://orbuc.io) for terms of use.
