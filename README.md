# TestWell Lab Tests — MCP server

Remote [Model Context Protocol](https://modelcontextprotocol.io) server for
[TestWell](https://www.test-well.com): cash-pay blood tests without insurance or a doctor's
visit, drawn at Quest Diagnostics, Labcorp and (in New York and New Jersey) BioReference.
No API key, no personal data stored by the read tools, prices live from the catalog of
record. Purchases complete on test-well.com (or, for platforms with a payment sheet,
through the checkout tools below).

- **Endpoint:** `https://www.test-well.com/mcp` (Streamable HTTP)
- **Docs:** https://www.test-well.com/developers · OpenAPI: https://www.test-well.com/openapi.json
- **Server card:** https://www.test-well.com/.well-known/mcp/server-card.json
- **Registry name:** `com.test-well/testwell` (version 1.2.0)
- **Contact:** support@test-well.com

## Connect

```bash
# Claude Code
claude mcp add --transport http testwell https://www.test-well.com/mcp
```

```json
// Cursor / Windsurf / any client that takes a JSON config
{ "mcpServers": { "testwell": { "url": "https://www.test-well.com/mcp" } } }
```

Claude.ai / Claude Desktop: Settings → Connectors → Add custom connector → `https://www.test-well.com/mcp`.
ChatGPT (developer mode): Settings → Connectors → Create → same URL.

## Tools

Read-only (annotated `readOnlyHint: true`):

| Tool | What it does |
| --- | --- |
| `search_tests` | Search 200+ tests and panels by keyword, biomarker, CPT code or goal |
| `get_test` | Full detail for one test or panel: per-lab prices, biomarkers, fasting, lab-quoted and measured turnaround, FAQs |
| `list_panels` | Panels by category or maximum price |
| `recommend_tests` | Tests and panels for stated goals or symptoms |
| `quote_order` | All-in total for a cart by US state: per-lab prices + the single $6 fee, prefilled cart link |
| `find_draw_sites` | Quest / Labcorp / BioReference patient service centers near a ZIP, with hours |
| `lookup_reference_range` | Physician-reviewed reference range for 130+ markers, sex/age bands, cited sources |
| `interpret_results` | Flag pasted lab values low / normal / high against those reviewed ranges (nothing stored) |
| `compare_prices` | All-in single-test price at TestWell vs every tracked provider, with verification dates |
| `compare_provider` | Head-to-head vs a named competitor (Quest Health, Labcorp OnDemand, JustLabs, Ulta Lab Tests, …) |
| `convert_units` | US ↔ SI conversion for 30 analytes; A1c NGSP ↔ IFCC with eAG |
| `calculate_free_testosterone` | Vermeulen free / bioavailable testosterone from total T, SHBG, albumin |
| `get_guide` | Any TestWell guide as Markdown |
| `get_checkout` | Current state of a checkout session (status, line items, totals) |

Agentic checkout (annotated `readOnlyHint: false`; each call is priced server-side and a
licensed physician authorizes the order before any lab work):

| Tool | What it does |
| --- | --- |
| `create_checkout` | Start an order: items, the US state of the draw, buyer name/email/phone → priced lines, the $6 fee, total, blocking messages and a `pay_online_url` prefilled cart |
| `update_checkout` | Change items, state or buyer details on an open session and get it re-priced |
| `cancel_checkout` | Cancel an open (unpaid) session |
| `complete_checkout` | For platforms that hold a payment credential (Stripe shared payment token): charge and create the order |

Reference content is reviewed by a named, licensed physician
(https://www.test-well.com/editorial-policy); it is educational, not a diagnosis.

## Example

```json
{ "jsonrpc": "2.0", "id": 1, "method": "tools/call",
  "params": { "name": "quote_order", "arguments": { "items": ["tsh", "ferritin"], "state": "FL" } } }
```

Returns the per-lab price of each item in Florida (Quest or Labcorp), the single $6 lab
processing fee, the all-in total, and a link that opens the same cart on test-well.com.

## Data behind the tools

- Catalog of record: https://www.test-well.com/api/catalog.json (per-lab prices, codes, turnaround)
- Blood Test Price Index (dated, multi-provider): https://www.test-well.com/research/blood-test-price-index
- Lab Turnaround Index (measured on real orders): https://www.test-well.com/research/turnaround-index
- Machine-readable site: https://www.test-well.com/llms.txt

## License

This repository holds documentation and the registry manifest only (MIT, see `LICENSE`).
The server itself is operated by TestWell and is not open source.
