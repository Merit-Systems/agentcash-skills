---
name: agent-data-tools
description: |
  590 small pay-per-call data tools behind one x402/MPP origin: read PDFs and web pages as text, screen crypto wallets against OFAC, token prices from Uniswap v3, chain reads on 18 EVM networks, news headlines, image description, cheap LLM calls, weather, company, email and domain checks, utilities. Every call is a GET with query parameters, most at $0.01, utilities from $0.002.

  USE FOR:
  - Reading a PDF or a web page as clean text or Markdown
  - Screening a wallet address or a name against the OFAC SDN list before paying
  - Token prices, wallet balances, gas, transactions and token transfers on Base, Ethereum, Arbitrum, Optimism, Polygon and 13 more
  - Current news headlines on a topic
  - Describing an image or reading the text in a screenshot
  - A cheap LLM answer (Llama 4 Scout at $0.002) or embeddings
  - Weather, holidays, exchange rates, company, email and domain facts
  - JSON, hashing, dates, validation and other utilities without a provider account

  TRIGGERS:
  - "pdf to text", "read this PDF", "extract the text from"
  - "scrape", "page to markdown", "read this page"
  - "sanctions", "OFAC", "is this wallet safe to pay"
  - "token price", "wallet balance", "gas price", "transaction status"
  - "latest news", "headlines"
  - "describe this image", "what does this screenshot say"
  - "weather in", "exchange rate", "public holidays"
  - "verify email", "whois", "company lookup"

  Use `npx agentcash@latest fetch` with the tool URL and its query parameters. Run `npx agentcash@latest check <url>` first when unsure of a parameter name; the full catalog with prices and schemas is https://agent-data-tools.revmesh2074.workers.dev/openapi.json.
metadata:
  version: 1
---

# Agent Data Tools with x402 APIs

One origin, 590 GET tools, one price per successful call; failed calls (4xx/5xx) are never charged. Pays over x402 (USDC on Base, Polygon, Arbitrum or Solana) or MPP (Tempo or Base); agentcash picks the method.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Quick Reference

| Task | Endpoint | Price | Returns |
|------|----------|-------|---------|
| PDF to text | `https://agent-data-tools.revmesh2074.workers.dev/pdf/text?url=…` | $0.005 | Text page by page, title, author, page count |
| Page to Markdown | `https://agent-data-tools.revmesh2074.workers.dev/extract?url=…` | $0.002 | Clean Markdown or text, links, tables, code blocks |
| Rendered page | `https://agent-data-tools.revmesh2074.workers.dev/extract/rendered?url=…` | $0.01 | JavaScript-rendered page as text or Markdown |
| OFAC screening | `https://agent-data-tools.revmesh2074.workers.dev/sanctions/check?address=…` (or `name=`) | $0.009 | block / review / pass with SDN matches |
| Counterparty check | `https://agent-data-tools.revmesh2074.workers.dev/check/counterparty?address=…` | $0.01 | Sanctions plus account type, activity and holdings in one verdict |
| Token price | `https://agent-data-tools.revmesh2074.workers.dev/price/token?symbol=WETH&chain=base` | $0.01 | USD price from Uniswap v3 pools |
| Several prices | `https://agent-data-tools.revmesh2074.workers.dev/price/tokens?symbols=WETH,USDC` | $0.005 | Up to 20 tokens in one call |
| Wallet portfolio | `https://agent-data-tools.revmesh2074.workers.dev/wallet/portfolio?address=0x…` | $0.005 | Holdings valued in USD |
| Chain reads | `https://agent-data-tools.revmesh2074.workers.dev/<chain>/balance`, `/tx`, `/receipt`, `/token-balance`, `/logs`, `/gas` | $0.01 | 18 EVM chains: base, eth, arb, op, polygon, avax, gnosis, linea, zksync, scroll, mantle, celo, sonic, unichain, hyperevm, bera, ink, sei |
| Base gas | `https://agent-data-tools.revmesh2074.workers.dev/base-gas` | $0.002 | Gas price and EIP-1559 fee estimate |
| News headlines | `https://agent-data-tools.revmesh2074.workers.dev/news?q=…` | $0.01 | Title, URL, snippet, publisher, age (Brave news index) |
| Web search | `https://agent-data-tools.revmesh2074.workers.dev/search?q=…` | $0.01 | Top pages with title, URL and snippet |
| Describe image | `https://agent-data-tools.revmesh2074.workers.dev/describe?url=…` (`question=` optional) | $0.01 | Description or an answer about the image |
| LLM chat | `https://agent-data-tools.revmesh2074.workers.dev/ai/chat?prompt=…` | $0.002 | Llama 4 Scout answer; OpenAI-compatible at `/v1/chat/completions` |
| Embeddings | `https://agent-data-tools.revmesh2074.workers.dev/ai/embed?text=…` | $0.01 | 1,024-dimension BGE-M3 vector |
| Weather | `https://agent-data-tools.revmesh2074.workers.dev/weather?city=London` | $0.01 | Current weather and 48-hour forecast |
| Email check | `https://agent-data-tools.revmesh2074.workers.dev/email?address=…` | $0.01 | Syntax, MX and disposable-domain checks |
| Domain | `https://agent-data-tools.revmesh2074.workers.dev/domain?name=example.com` | $0.01 | DNS, RDAP registration age, email authentication |
| Company | `https://agent-data-tools.revmesh2074.workers.dev/company?name=…` | $0.01 | Profile, identifiers and website from Wikidata |
| SEC filings | `https://agent-data-tools.revmesh2074.workers.dev/sec/filings?ticker=AAPL` | $0.01 | 10-K, 10-Q, 8-K and Form 4 from EDGAR |
| Exchange rates | `https://agent-data-tools.revmesh2074.workers.dev/fx?from=EUR&to=USD` | $0.01 | ECB reference rates and conversion |
| Prediction market odds | `https://agent-data-tools.revmesh2074.workers.dev/polymarket/markets?q=…` | $0.01 | Polymarket markets with outcome prices, bid/ask, volume; also `/polymarket/events`, `/orderbook`, `/price-history` |
| Historical balances | `https://agent-data-tools.revmesh2074.workers.dev/wallet/balance-history?address=0x…&days=7` | $0.01 | A Base wallet's ETH and token balances once a day, valued in USD |
| All FX pairs | `https://agent-data-tools.revmesh2074.workers.dev/fx/all` | $0.01 | Cross rates among the major currencies in one call |
| Holidays | `https://agent-data-tools.revmesh2074.workers.dev/holidays?country=SE&year=2026` | $0.01 | A country's public holidays |

Everything else (JSON, hashing, dates, validators, QR codes, Hyperliquid market data, World Bank series, packs that run several tools in one call): https://agent-data-tools.revmesh2074.workers.dev/tools, and one guide per job at https://agent-data-tools.revmesh2074.workers.dev/guides.

## Examples

Read a PDF:

```bash
npx agentcash@latest fetch "https://agent-data-tools.revmesh2074.workers.dev/pdf/text?url=https://arxiv.org/pdf/1706.03762&firstPage=1&pages=2"
```

Screen a wallet before paying it:

```bash
npx agentcash@latest fetch "https://agent-data-tools.revmesh2074.workers.dev/sanctions/check?address=0x098B716B8Aaf21512996dC57EB0615e2383E2f96"
```

Token price and a cheap LLM call:

```bash
npx agentcash@latest fetch "https://agent-data-tools.revmesh2074.workers.dev/price/token?symbol=WETH&chain=base"
npx agentcash@latest fetch "https://agent-data-tools.revmesh2074.workers.dev/ai/chat?prompt=Summarize%20x402%20in%20one%20sentence"
```

## Notes

- Inputs are query parameters; common aliases work (`wallet` for `address`, `tx` for `hash`, `query` for `q`). Unsure of a name: `npx agentcash@latest check <url>`.
- Add `only=field1,field2` to any call to get just those top-level fields back.
- Prices are fixed per tool and listed in `x-payment-info` in the OpenAPI document; the 402 carries the same terms.
- Reliability per tool is public at https://agent-data-tools.revmesh2074.workers.dev/status.

See [rules/choosing-an-endpoint.md](rules/choosing-an-endpoint.md) for which tool fits which task.
