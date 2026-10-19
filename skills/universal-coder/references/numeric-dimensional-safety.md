# Numeric / Dimensional Safety Auditing

Use for: codebases doing arithmetic across mixed units, precisions, or decimal scaling —
DeFi/financial protocols, fixed-point math, scientific computation with physical units.

## When to use

- Reviewing code with token amounts, prices, exchange rates, or any "amount in X per Y" math.
- Fixed-point/decimal arithmetic where values are scaled by `10^n` (e.g. 18-decimal tokens).
- Scientific code mixing physical units (meters vs. feet, seconds vs. milliseconds).

## When NOT to use

- Pure integer counting (loop indices, array lengths) with no physical/financial dimension.
- A single formula spot-check — just read the code directly instead of a full audit pass.

## Core technique: dimensional annotation

Annotate every numeric variable that carries a unit or scaling factor with its dimension,
right next to the declaration or at the point it's computed, e.g.:

```solidity
uint256 amountD18; // D18{tok} — token amount, 18 decimals
uint256 priceD8;   // D8{UoA/tok} — price in units-of-account per token, 8 decimals
```

Then check every arithmetic operation against dimensional algebra rules:

- **Addition/subtraction** is only valid between values of the *same* dimension and scale.
  `amountD18 + priceD8` is a bug even if both compile — the scales don't match.
- **Multiplication** combines dimensions and *adds* decimal scales: `D18{tok} * D8{UoA/tok} =
  D26{UoA}` — if the code then treats that result as `D18`, that's a missing descale and a
  real bug.
- **Division** subtracts decimal scales and divides dimensions correspondingly.

## Workflow

1. **Scan** the codebase for arithmetic-heavy files; prioritize anything touching balances,
   prices, exchange rates, or oracle outputs.
2. **Establish vocabulary**: list every distinct unit/dimension in play (token amount, USD
   price, shares, interest rate, time duration) and its expected decimal scale.
3. **Annotate** each variable at its point of origin (function parameter, storage read, oracle
   call) with its dimension and scale.
4. **Propagate** the annotation through each arithmetic expression, checking consistency at
   every `+`, `-`, `*`, `/`.
5. **Flag mismatches**: any operation where the dimensional algebra doesn't balance is a
   candidate bug — report it with the exact expression and what scale it should be vs. what it
   implicitly is.

## Common bug patterns

- Mixing a token's native decimals (6, 8, 18 are all common) without normalizing before summing
  balances across different tokens.
- Forgetting to descale after a multiply (classic `x * y / 1` instead of `x * y / 1e18`).
- Treating an oracle price feed's decimals as a fixed constant when it's actually
  feed-dependent.
- Rounding direction inconsistency (always round in the protocol's favor, consistently, or
  value leaks accumulate).

## Escalating beyond this file

This is a manual annotate-and-check technique for a single pass or a small formula. A full
repository-wide sweep with persisted coverage tracking and automated propagation across every
arithmetic expression is a bigger, stateful job than one context window should attempt by
hand. If a dedicated dimensional-analysis skill with that orchestration is installed and
available, use it for whole-codebase passes — see `escalation-and-scope.md`.
