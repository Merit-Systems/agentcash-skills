---
name: token-security
description: |
  Pre-swap token security, honeypot detection, bytecode CFG audit, and pre-sign transaction firewall across 7 chains (Base, Ethereum, Arbitrum, Optimism, Polygon, BSC, Solana + Token-2022) using x402-protected APIs at api.nullclear.dev.

  USE FOR:
  - Checking any EVM or Solana token for honeypots, hidden buy/sell taxes, or malicious proxies before swapping
  - Getting an A-F token grade, one-line bytecode verdict, and shareable SVG grade card
  - Auditing raw EVM calldata, Multicall3/Universal Router bundles, EIP-712 Permits, or Solana wire transactions before signing
  - Running stateful transaction simulation (eth_simulateV1 / Solana simulateTransaction) to catch drainers and reverts
  - Generating an institutional 7-chain due-diligence dossier with EIP-712 clearance certificate

  TRIGGERS:
  - "is this token safe", "honeypot check", "token security", "token audit", "rug check"
  - "token verdict", "grade this token", "buy tax", "sell tax", "proxy check"
  - "simulate transaction", "decode calldata", "check permit", "drainer check", "pre-sign guard"
  - "due diligence", "audit contract", "before I swap", "check this Base token", "check this Solana mint"

  Use `npx agentcash@latest fetch` for api.nullclear.dev endpoints. Always enforce `recommendedAgentAction === "EXECUTE_SAFE"` before signing or swapping.
metadata:
  version: 1
---

# Token Security & Pre-Sign Firewall with x402 APIs

Audit tokens, smart contracts, and unsigned transactions across **Base, Ethereum, Arbitrum, Optimism, Polygon, BSC, and Solana (including Token-2022)** through x402-protected endpoints at `api.nullclear.dev`.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and wallet setup.

## Mandatory Workflow

Before any `api.nullclear.dev` call:

1. `npx agentcash@latest discover https://api.nullclear.dev`
2. `npx agentcash@latest check <endpoint-url>` — confirm query/body parameters and exact USDC price
3. `npx agentcash@latest fetch` with the exact path and schema from step 2

ALWAYS use `npx agentcash@latest fetch` for `api.nullclear.dev` endpoints — never curl or WebFetch.

## IMPORTANT: Never Guess Endpoint Paths

All NullClear x402 endpoints live under `/v1/bazaar/*`:

| Wrong (guessed) | Correct |
|-----------------|---------|
| `/api/token-security` | `https://api.nullclear.dev/v1/bazaar/token-security` |
| `/v1/token-verdict` | `https://api.nullclear.dev/v1/bazaar/token-verdict` |
| `/api/guard` | `https://api.nullclear.dev/v1/bazaar/guard` |
| `/v1/due-diligence` | `https://api.nullclear.dev/v1/bazaar/due-diligence` |

## Quick Reference

| Task | Endpoint | Method | Price | Best For |
|------|----------|--------|-------|----------|
| Token security scan | `https://api.nullclear.dev/v1/bazaar/token-security` | GET / POST | $0.005 | Bytecode CFG, honeypot, buy/sell tax, EIP-1967 proxy, liquidity |
| Token verdict + card | `https://api.nullclear.dev/v1/bazaar/token-verdict` | GET / POST | $0.005 | A-F grade, concrete 1-line bytecode verdict & shareable SVG card |
| Pre-sign tx firewall | `https://api.nullclear.dev/v1/bazaar/guard` | POST / GET | $0.005 | Nested calldata/EIP-712/Solana wire decode + stateful simulation |
| Institutional dossier | `https://api.nullclear.dev/v1/bazaar/due-diligence` | POST / GET | $0.10 | Full 7-chain contract + proxy/ABI/CFG + signed EIP-712 certificate |
| Batch token scan | `https://api.nullclear.dev/v1/bazaar/scan-batch` | POST | $0.025 | Up to 15 tokens across 7 chains in 1 call |

See [rules/when-to-use.md](rules/when-to-use.md) for decision guidance.

## Supported Chains

`base`, `eth`, `arbitrum`, `optimism`, `polygon`, `bsc`, `solana` (auto-detected for base58 SPL mints when omitted).

## Token Security Scan ($0.005)

Check any ERC-20 contract or Solana SPL / Token-2022 mint for honeypot traps, hidden buy/sell taxes, proxy upgradeability, and DEX liquidity:

```bash
# Base ERC-20 (USDC example)
npx agentcash@latest fetch "https://api.nullclear.dev/v1/bazaar/token-security?chain=base&address=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913" -m GET
```

```bash
# Solana SPL / Token-2022 (USDC mint example)
npx agentcash@latest fetch "https://api.nullclear.dev/v1/bazaar/token-security?chain=solana&address=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v" -m GET
```

**Query parameters:**
- `chain` — `base` (default), `eth`, `arbitrum`, `optimism`, `polygon`, `bsc`, or `solana`
- `address` — EVM `0x...` contract address or Solana base58 mint address

**Returns:** `securityScore` (0–100), `recommendedAgentAction`, `isHoneypot`, `buyTaxBps`, `sellTaxBps`, `isProxy`, `implementationAddress`, `liquidityUsd`, `evidence` (CFG `hasSelfDestruct`, `hasDelegateCall`, `dangerousSelectorsFound`, Solana mint/freeze/permanent-delegate authorities), and SHA-256 `attestation`.

## Token Verdict + Shareable Card ($0.005)

Get a crisp `A`–`F` grade, a concrete one-line bytecode/tax/proxy verdict, and a public Edge-cached SVG card URL:

```bash
npx agentcash@latest fetch "https://api.nullclear.dev/v1/bazaar/token-verdict?chain=base&address=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913" -m GET
```

**Returns:** `grade` (`A|B|C|D|F`), `securityScore`, `oneLineVerdict`, `recommendedAgentAction` (`EXECUTE_SAFE | CAUTION_HIGH_TAX | BLOCK_HONEYPOT`), `evidence`, `scorecardUrl`, and `shareCardImageUrl` (`https://api.nullclear.dev/v1/verdict-card/{chain}/{address}.svg`).

## Pre-Sign Transaction & Approval Firewall ($0.005)

Recursively unwraps Multicall3 / Universal Router / Safe calldata, audits EIP-712 Permits / Seaport orders, parses Solana wire transactions, simulates stateful asset diffs (`eth_simulateV1`), and scans touched tokens before signing:

```bash
npx agentcash@latest fetch https://api.nullclear.dev/v1/bazaar/guard -m POST -b '{
  "chain": "base",
  "from": "0xfd74fF802EA0E735514C070AF04E0D6528deD145",
  "to": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
  "data": "0x095ea7b3000000000000000000000000b586efcca41df27ad3873beca54da0c30bf04d5c00000000000000000000000000000000000000000000000000000000000f4240",
  "value": "0x0"
}'
```

**Body fields:**
- `chain` — `base` (default), `eth`, `arbitrum`, `optimism`, `polygon`, `bsc`, or `solana`
- `from` — sender wallet address (recommended for accurate simulation)
- `to` — target contract address (EVM)
- `data` — `0x`-prefixed hex calldata
- `value` — optional wei value (`"0x0"` default)
- `typedData` — optional EIP-712 typed data JSON (Permit / Permit2 / Seaport)
- `transaction` — optional Base64-encoded Solana wire transaction

## Institutional Due-Diligence Dossier ($0.10)

Combines 7-chain token security, proxy/ABI/CFG disassembly, historical/deployer synthesis, and a verifiable EIP-712 Clearance Certificate:

```bash
npx agentcash@latest fetch https://api.nullclear.dev/v1/bazaar/due-diligence -m POST -b '{
  "chain": "base",
  "address": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913"
}'
```

## Decision Policy for Autonomous Agents

Always gate execution on `recommendedAgentAction`:
- `EXECUTE_SAFE`: Safe to proceed.
- `CAUTION_HIGH_RISK` / `CAUTION_HIGH_TAX`: Warn user or tighten slippage/approval caps before proceeding.
- `ABORT_CRITICAL_TRAP` / `BLOCK_HONEYPOT` / `ABORT_DRAINER_APPROVAL` / `ABORT_WILL_REVERT`: Hard abort — do **not** sign or broadcast.
