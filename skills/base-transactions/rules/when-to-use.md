# Choosing between the endpoints

## tx-decision vs. free RPC

`eth_feeHistory` is free and gives you raw numbers. This endpoint gives you the
decision: EIP-1559 sizing with headroom, a submit-or-wait verdict against the
current window, the USD cost for the operation you are actually doing, and a
backoff interval. If your agent already has fee logic it trusts, use the free
RPC — this is for loops that would otherwise hardcode a gas price.

Call it **before every send** when your agent submits transactions in a loop.
One call per transaction, not one per session: the answer changes with the
block.

## finality-check vs. counting confirmations

Counting confirmations is a heuristic. This reads Base's own `safe` and
`finalized` block tags, which reflect what L1 has actually attested to and
finalized — a different question from "how many blocks since".

Call it **after submitting**, before you do something you cannot undo: releasing
goods, crediting an account, marking an invoice paid. `safe` is enough for most
commerce; wait for `finalized` when reversal would be expensive.

## The Pulse briefing

The $0.05 composite is a full market briefing — congestion, trend, ETH price,
settlement costs across operation types, one settle-now verdict. It answers
"what is the network doing right now", not "what should this transaction do".

Use it for a periodic check (a morning report, a dashboard refresh), not in a
per-transaction loop. `https://x402-mcp.onrender.com/pulse` is a free preview of
the same shape, so try that before paying for the composite.

## Cost sanity

At $0.01 per call, an agent submitting 100 transactions a day spends $1/day on
tx-decision. If your transactions are worth less than a few cents each, the
decision costs more than the fee it optimizes — go with the free RPC.
