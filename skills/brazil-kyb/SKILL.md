---
name: brazil-kyb
description: |
  Verify any Brazilian company (CNPJ) with official data via x402-paid Brazilayer APIs: registry status, partners, sanctions (5 government lists), Central Bank licence, public contracts, investment funds. $0.001 to $0.05 per call, no API keys.

  USE FOR:
  - KYB/onboarding of a Brazilian counterparty (does the CNPJ exist, is it active, who owns it)
  - Sanctions and debarment screening of a Brazilian company
  - Checking whether a company is a licensed financial institution in Brazil
  - Public procurement history (contracts won) and fund management data
  - Full enrichment of a CNPJ in one call

  TRIGGERS:
  - "CNPJ", "Brazilian company", "empresa brasileira", "Receita Federal"
  - "is this Brazilian company real", "sanctions Brazil", "KYB Brazil"
  - "who are the partners of", "sócios", "licitação", "BCB licence"
metadata:
  version: 1.0
---

# Brazil KYB (CNPJ) via x402

Use this skill when a task involves a **Brazilian company**: onboarding a supplier or customer,
checking whether a CNPJ exists and is active, finding who the partners (sócios) are, screening
sanctions and debarment lists, confirming a bank or payment institution licence, looking at
government contracts, or verifying an investment fund. The data comes from the official Brazilian
sources (Receita Federal, CGU/Portal da Transparência, Banco Central, PNCP, CVM) and is served by
`api.brazilayer.com`, an x402 service (pay per call in USDC on Base, no API key, no signup).

## Inputs

- **CNPJ**: 14 digits, with or without punctuation (`00.000.000/0001-91` = `00000000000191`).
  Validate the check digits for free before paying: `GET https://api.brazilayer.com/v1/ferramentas/cnpj/<cnpj>`.
- If you only have a name, search first (paid, US$ 0.01):
  `GET https://api.brazilayer.com/v1/cnpj/busca?q=<name>`.

## Free, no payment

- `GET https://api.brazilayer.com/v1/cnpj/amostra` and `/v1/integridade/amostra`: sample responses to see the shape.
- `GET https://api.brazilayer.com/v1/ferramentas/cnpj/<cnpj>`: check-digit validation only (does not say whether the company exists).
- `GET https://api.brazilayer.com/.well-known/x402`: every paid route with price.

## Paid routes (USDC on Base, via x402)

Cheapest first. Pay with your x402 wallet; with agentcash, call the `fetch` tool on the URL and
payment is automatic.

| Need | Route | Price |
|---|---|---|
| Does it exist and is it active? | `GET /v1/cnpj/situacao/<cnpj>` | US$ 0.001 |
| Full registry record (name, status, CNAE, address, capital) | `GET /v1/cnpj/empresa/<cnpj>` | US$ 0.005 |
| Partners and administrators (QSA) | `GET /v1/cnpj/empresa/<cnpj>/socios` | US$ 0.005 |
| Sanctions and debarment (CEIS, CNEP, CEPIM, leniency, slave-labour list) | `GET /v1/integridade/consulta/<cnpj>` | US$ 0.01 |
| Central Bank licence (bank, payment institution, fintech) | `GET /v1/bcb/instituicao/<cnpj>` | US$ 0.005 |
| Federal contracts won | `GET /v1/licitacoes/fornecedor/<cnpj>` | US$ 0.01 |
| Investment fund check (CVM) | `GET /v1/fundos/consulta/<cnpj>` | US$ 0.005 |
| **Everything above in one call** (registry + partners + sanctions + BCB + contracts) | `GET /v1/empresa/enriquecer/<cnpj>` | US$ 0.05 |

Base URL: `https://api.brazilayer.com`. Example:

```
fetch https://api.brazilayer.com/v1/empresa/enriquecer/00000000000191
```

## How to answer

1. Start with `situacao` (US$ 0.001) unless the user clearly needs the full profile; then escalate.
2. Report the registry status with its date, and say explicitly whether the company appears on any
   sanctions list (the response says "não consta" when it does not).
3. Keys are in Portuguese legal terms (`razao_social`, `situacao_cadastral`, `cnae_principal`,
   `qsa`); translate them for the user, keep the original values.
4. Registry data is the monthly Receita Federal release; sanctions refresh daily; state the
   `atualizado_em` / reference date from the response when it matters.
5. Every response includes `veja_tambem` with the next useful route and its price; mention it only
   if the user's task needs it.

## Errors

- `402`: pay (the body explains the price and the free sample). Paid routes ask for payment
  before validating the CNPJ, so run the free check-digit validation first and never pay for a
  malformed number.
- `404`: the CNPJ is not in the registry (it may be brand new or invalid).

Docs: https://api.brazilayer.com/docs · terms: https://api.brazilayer.com/termos ·
ratings of this and every x402 service: https://agenteconomy.report/s/api.brazilayer.com
