# Choosing an Endpoint

Four paid routes answer four different questions. Picking the wrong one either overpays or returns data that does not answer the user's actual question.

## Map the question to the route

| The user is asking | Route | Why |
|--------------------|-------|-----|
| "What is gas right now?" | `/gas` | A single point-in-time reading |
| "How much will this cost?" | `/gas` with `gasLimit` | The estimate comes back already priced for the operation |
| "Is gas high or low?" | `/gas/history` | Requires a baseline to compare against |
| "Should I send now or wait?" | `/gas/history` then `/gas/cheapest-window` | The verdict answers now; the window answers when |
| "Which chain is cheapest?" | `/gas/compare` | Reads four chains in one paid call |
| "Is bridging to Base worth it?" | `/gas/compare` | `baseVsEthereum` states the multiplier directly |
| "When should I schedule this?" | `/gas/cheapest-window` | Hour-of-day ranking |
| "Do you have enough data?" | `/health` | Free, no payment |

## Do not substitute `/gas` for history

`/gas` reports the current gas price. It cannot say whether that price is high or low, because a single reading has no context. If the user's question contains a comparison ("cheap", "high", "spike", "trend", "worth waiting"), the answer needs `/gas/history`.

## Do not loop `/gas` to build history

Calling `/gas` repeatedly to assemble a time series costs $0.005 per sample and produces worse data than `/gas/history`, which is backed by continuous sampling that already happened. One `/gas/history` call at $0.01 replaces an unbounded number of `/gas` calls.

## Do not loop `/gas` across chains

`/gas` only covers Base. For multi-chain questions use `/gas/compare`: one call, four chains, $0.01.

## Check coverage before paying for history

`/health` is free and reports how many samples exist and how many hours they span. The history endpoints depend on that buffer.

- Coverage under 1 hour: history is not yet meaningful. Use `/gas` instead.
- Coverage under 24 hours: `/gas/history` works, but `/gas/cheapest-window` cannot rank a full day yet.
- Coverage 24 hours or more: both history routes are meaningful.

Every paid history response also embeds a `coverage` object, so the depth of the underlying data is visible after the fact as well.

## Interpreting `verdict`

`/gas/history` returns `verdict` as one of:

| Value | Meaning |
|-------|---------|
| `cheap` | Current price is in the bottom third of the window's range |
| `normal` | Middle third |
| `expensive` | Top third |
| `flat` | The window showed no meaningful variation |

The verdict is relative to the `hours` window requested. A price can be `cheap` against 24 hours and `expensive` against 7 days, so state the window when reporting it to the user.

## Interpreting `cheapestHourUtc`

Hours are bucketed in **UTC**, not local time. Convert before telling the user when to transact, and say which timezone you converted to.

`savingsPercent` compares the cheapest hour against the priciest hour in the window. It is the upper bound on savings from perfect timing, not a guarantee.
