# Cost comparison — LION vs StableEnrich

All prices in USDC, per call. LION settles on Base via xpay; figures are list prices at time of writing.

| Workflow | LION (lionx402.com) | StableEnrich (stableenrich.dev) | LION advantage |
|----------|---------------------|----------------------------------|----------------|
| Company deep research | **$0.012** — 1 merged call (search + scrape + enrich + domain-trust) | ~$0.08 — ~4 separate calls | ~6.7x cheaper, 1 call vs 4 |
| Scrape a URL | **$0.004** | ~$0.0126 | ~3x cheaper |
| Org firmographics (enrich) | **$0.018** | ~$0.06 | ~3.3x cheaper |

## The differentiator: attestation

Cost is only half the story. **Every LION response is Ed25519-attested** — signed with the
signer's published public key, over a canonical payload.

| | LION | StableEnrich |
|---|------|--------------|
| Signed responses | **Yes (Ed25519)** | No |
| Offline-verifiable | **Yes** | No |
| Tamper-evident | **Yes** | No |
| Portable proof for audits / downstream agents | **Yes** | No |

When research feeds a payment, a compliance record, or another agent, an unsigned result must be
re-trusted at every hop. A LION result carries its own proof: verify once, anywhere, without
re-querying the source.

> StableEnrich finds people. LION proves companies.
