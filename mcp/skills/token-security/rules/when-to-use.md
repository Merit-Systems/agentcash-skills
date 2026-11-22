# When to Use NullClear Token Security Endpoints

## Decision Tree

```
Evaluating an on-chain token or transaction?
|
+-- Need an A-F grade, 1-line summary & shareable SVG card?
|   --> GET /v1/bazaar/token-verdict ($0.005)
|
+-- Need full bytecode CFG, honeypot, tax bps, proxy & Solana Token-2022 telemetry?
|   --> GET /v1/bazaar/token-security ($0.005)
|
+-- Screening a watchlist or basket of 3-15 tokens at once?
|   --> POST /v1/bazaar/scan-batch ($0.025)
|
+-- About to sign calldata, a swap, an approval, an EIP-712 Permit, or a Solana tx?
|   --> POST /v1/bazaar/guard ($0.005)
|
+-- Treasury allocation, governance proposal, or compliance sign-off needing an EIP-712 Clearance Certificate?
    --> POST /v1/bazaar/due-diligence ($0.10)
```

## Endpoint Comparison

| Scenario | Recommended Endpoint | Price | Key Output |
|----------|----------------------|-------|------------|
| Pre-swap honeypot & tax check | `/v1/bazaar/token-security` | $0.005 | `isHoneypot`, `buyTaxBps`, `sellTaxBps`, `isProxy`, `recommendedAgentAction` |
| Human-readable verdict or social share card | `/v1/bazaar/token-verdict` | $0.005 | `grade`, `oneLineVerdict`, `shareCardImageUrl` |
| Pre-sign calldata / Permit2 / approval check | `/v1/bazaar/guard` | $0.005 | Decoded nested calls, simulated asset diffs, `ABORT_DRAINER_APPROVAL` |
| Multi-token screening (up to 15) | `/v1/bazaar/scan-batch` | $0.025 | Per-token verdicts + aggregated risk summary |
| Institutional audit & signed certificate | `/v1/bazaar/due-diligence` | $0.10 | Full contract + ABI/CFG + EIP-712 `clearanceCertificate` |

## Best Practices

1. **Always check before signing**: Run `/v1/bazaar/guard` on the exact `to` + `data` + `from` before broadcasting any swap or approval.
2. **Never trust market liquidity alone**: High DexScreener liquidity can still hide 100% sell taxes, `SELFDESTRUCT`, or Solana Token-2022 permanent-delegate / freeze authorities.
3. **Batch when scanning watchlists**: Scanning 15 tokens via `/v1/bazaar/scan-batch` ($0.025) costs 67% less than 15 individual scans ($0.075).
4. **Fail closed on `ABORT_*` / `BLOCK_HONEYPOT`**: Never override a critical trap verdict automatically.
