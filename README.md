# Litecoin Transaction Graph Map

A **zero-dependency, browser-based** Litecoin address explorer. Paste one or more LTC addresses, fetch transaction data from multiple free public APIs in parallel, and explore the results in a unified table alongside an interactive Obsidian-style network graph — all without installing anything.

---

## Features

- **Multi-source data fetching** — queries four free public APIs simultaneously and merges results:
  - [BlockCypher](https://www.blockcypher.com/)
  - [Chain.so](https://chain.so/)
  - [Blockchair](https://blockchair.com/)
  - [LitecoinSpace](https://litecoinspace.org/) (with cursor-based pagination)
- **Deduplication** — transactions seen across multiple endpoints are merged into a single row and tagged with all sources that reported them.
- **AllOrigins CORS proxy fallback** — optional toggle to retry CORS-blocked requests through the [AllOrigins](https://allorigins.win/) proxy.
- **Configurable limits** — control max transactions per endpoint, graph node cap, and per-request timeout from the UI.
- **Unified transaction table** — scrollable table showing address, TxID, timestamp, confirmations, value (LTC), and which endpoint(s) returned it.
- **Interactive network graph** — Obsidian-style canvas graph with:
  - Drag nodes, pan canvas, pinch/scroll to zoom
  - Address nodes (gold) and transaction nodes (blue)
  - Coloured directed edges for incoming (teal) and outgoing (red) flows
  - Cluster mode to group nodes by address
  - Address / TxID search with optional hide-non-matches filter
  - Click any node to inspect details and copy addresses or TxIDs
  - Reset View button
- **Endpoint status panel** — live log of every API call result (success / error) with status codes.
- **Fully client-side** — a single `index.html` file; no server, no build step, no API keys required.

---

## Getting Started

### Option 1 — Open directly in your browser

1. Download or clone this repository.
2. Open `index.html` in any modern browser (Chrome, Firefox, Edge, Safari).

```bash
git clone https://github.com/Harshit001kumar/litecoin-transaction-graph-map.git
cd litecoin-transaction-graph-map
# macOS
open index.html
# Linux
xdg-open index.html
# Windows
start index.html
```

### Option 2 — Serve locally (avoids some browser security restrictions)

```bash
# Python 3
python -m http.server 8080
# then visit http://localhost:8080
```

---

## Usage

1. **Paste LTC addresses** (one per line) into the *LTC addresses* text area.  
   Both legacy (`L…`) and SegWit (`ltc1…`) formats are supported.
2. *(Optional)* Adjust the settings:
   | Setting | Default | Description |
   |---|---|---|
   | Max transactions per endpoint | 150 | Upper limit of transactions fetched from each API |
   | Graph tx node cap | 260 | Maximum transaction nodes rendered in the graph |
   | Request timeout (ms) | 12 000 | Per-request abort timeout |
   | AllOrigins proxy | Off | Retry CORS-blocked requests through a proxy |
3. Click **Fetch Transactions**.
4. Results appear in the **Unified Transactions** table and the **Network Map** simultaneously.
5. Use the graph controls to search, filter, cluster, zoom, and inspect nodes.

---

## Project Structure

```
litecoin-transaction-graph-map/
├── index.html   # Entire application (HTML + CSS + JavaScript)
└── README.md
```

All logic lives in `index.html`:
- **`endpointConfigs`** array — one entry per API with `buildUrls`, `parse`, and (for LitecoinSpace) a `fetchPaged` method.
- **`fetchJson` / `fetchWithTimeout`** — fetch helpers with timeout and optional proxy support.
- **`mergeTransactions`** — deduplicates rows by `address+txid` key.
- **`graph` object** — canvas-based force-layout renderer with pan/zoom, node picking, and cluster mode.

---

## API Endpoints Used

| Provider | Endpoint pattern | Notes |
|---|---|---|
| BlockCypher | `api.blockcypher.com/v1/ltc/main/addrs/{addr}/full` | Up to 200 txs per call |
| Chain.so | `chain.so/api/v2/get_tx_received/LTC/{addr}` + `get_tx_spent` | Two calls per address |
| Blockchair | `api.blockchair.com/litecoin/dashboards/address/{addr}` | Up to 100 txs |
| LitecoinSpace | `litecoinspace.org/api/address/{addr}/txs` | Paginated via cursor |

All APIs are free-tier and require no API key for basic usage. Rate limits may apply.

---

## Browser Compatibility

Requires a browser that supports:
- ES2020+ (`async/await`, `Map`, `Set`, optional chaining)
- Canvas 2D API
- Fetch API with `AbortController`

Any recent version of Chrome, Firefox, Edge, or Safari will work.

---

## License

This project is open source. See the repository for license details.

