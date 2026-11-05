# Choosing an endpoint

| Scenario | Tool | Price |
|----------|------|-------|
| A PDF by URL | `/pdf/text?url=…&firstPage=1&pages=20` | $0.005 |
| A web page as served | `/extract?url=…&format=markdown` | $0.002 |
| A page that needs a browser (JavaScript) | `/extract/rendered?url=…` | $0.01 |
| Several pages in one call | `/contents?urls=…,…` (up to 5) | $0.01 |
| Find pages first | `/search?q=…` then `/extract` | $0.01 + $0.002 |
| Is this address sanctioned? | `/sanctions/check?address=…` | $0.009 |
| Should my agent pay this address at all? | `/check/counterparty?address=…` | $0.01 |
| Price of one token | `/price/token?symbol=…&chain=…` (symbol, 0x address, `coingecko:bitcoin` or a coin name) | $0.01 |
| Prices of many tokens | `/price/tokens?symbols=…` | $0.005 |
| What a wallet holds | `/wallet/portfolio?address=…` (Base) or `/wallet/balances?address=…` (5 chains) | $0.005 |
| Did a transaction succeed? | `/<chain>/receipt?hash=…` | $0.01 |
| Token transfers of an address | `/<chain>/token-transfers?address=…` | $0.01 |
| Gas right now | `/base-gas`, `/eth/gas`, `/pack/gas-tracker` (five chains) | $0.002-0.01 |
| Headlines on a topic | `/news?q=…&freshness=day` | $0.01 |
| Headlines with tone | `/pack/news-sentiment?q=…` | $0.01 |
| What is in an image | `/describe?url=…` or `?url=…&question=…` | $0.01 |
| A quick LLM answer | `/ai/chat?prompt=…` (Llama 4 Scout) | $0.002 |
| A stronger model | `/ai/chat-large?prompt=…` (Llama 3.3 70B or gpt-oss-120b) | $0.01 |
| Embeddings | `/ai/embed?text=…` | $0.01 |
| Weather | `/weather?city=…` | $0.01 |
| Exchange rate | `/fx?from=…&to=…&amount=…` | $0.01 |
| Public holidays | `/holidays?country=…&year=…` | $0.01 |
| Email address check | `/email?address=…`; a whole domain's mail setup: `/pack/email-deliverability?domain=…` | $0.01 |
| Domain facts | `/domain?name=…`; full report with trust verdict: `/domain-report?name=…` | $0.005 / $0.01 |
| Company | `/company?name=…` (a company name or its domain) | $0.01 |
| SEC filings | `/sec/filings?ticker=…&form=10-K` | $0.01 |
| Hyperliquid perps | `/hyperliquid/market?coin=BTC`, `/hyperliquid/funding-history`, `/hyperliquid/indicators` | $0.01 |
| Utilities (JSON, hash, UUID, dates, units, validators) | `/json/validate`, `/hash`, `/uuid`, `/date/diff`, `/units/convert`, `/validate/iban` … | $0.01 |

`<chain>` is one of base, eth, arb, op, polygon, avax, gnosis, linea, zksync, scroll, mantle, celo, sonic, unichain, hyperevm, bera, ink, sei. All chain reads: `/<chain>/balance`, `token-balance`, `token-info`, `block`, `gas`, `tx`, `receipt`, `nonce`, `is-contract`, `logs`, `token-transfers`, `nft-owner`, `nft-balance`, `nft-metadata`, `storage`, `proxy`, `call`, `estimate-gas`, `gas-history`, `allowance`.

All prices are per successful call; a failed call is not charged. Full list with inputs: https://agent-data-tools.revmesh2074.workers.dev/openapi.json
