# Price Discovery

_Author: Dylan, Avinasi Labs_

Token price rises continuously as investors participate in the IDO. Earlier participants receive more tokens per dollar than later ones, creating automatic rewards for early commitment without manual tiering or arbitrary price schedules.

The protocol uses Uniswap-style constant product pricing integrated with virtual reserves (see [virtual-amm.md](virtual-amm.md) for technical construction). This document focuses on pricing from the investor perspective.

## Trading Fees

The protocol charges fees on IDO transactions:

- **Purchase fee: 0.3%** - Applied to USDC before calculating tokens received. Example: 1,000 USDC pays 3 USDC fee, 997 USDC buys tokens
- **Sale fee: 0.5%** - Applied to USDC refund after constant product calculation

These fees go to protocol treasury (not the project) to support operations and discourage speculation.

## How Price Evolves

Token price equals the reserve ratio at any moment: $$P = \frac{x}{y}$$ where $$x$$ is accumulated USDC and $$y$$ is remaining tokens. As investors buy tokens, USDC accumulates and token supply depletes, causing continuous price appreciation.

**Example progression** for a project raising 50,000 USDC:

| Investment | $$x$$ (USDC) | $$y$$ (tokens) | Price (USDC/token) |
|-----------|-----------|-------------|-------------------|
| Start | 2,525 | 252,525 | 0.0100 |
| After 5,000 USDC | 7,525 | 84,842 | 0.0887 |
| After 10,000 USDC | 12,525 | 50,901 | 0.2461 |
| After 25,000 USDC | 27,525 | 23,157 | 1.1887 |
| After 50,000 USDC | 52,525 | 12,142 | 4.3255 |

Price appreciation from \$0.01 to \$4.00+ (400x) demonstrates the substantial early participation advantage.

## Price Impact

Investors don't pay the instantaneous marginal price—they pay an average price across their entire purchase. Larger purchases consume more token supply, paying progressively higher prices.

For example, at a marginal price of \$0.100, a small 100 USDC purchase might pay an average of \$0.101 per token (1% slippage), while a large 1,000 USDC purchase pays \$0.110 average (10% slippage). This price impact naturally discourages whale accumulation and encourages distributed ownership.

## Why Early Participation Matters

The timing advantage is deterministic and mathematical. Investors participating in the first 10% of fundraising purchase near \$0.01 when token supply is abundant, receiving maximum tokens per USDC. Middle participants pay moderately higher prices but still receive favorable terms. Late participants purchasing near completion pay close to launch prices, receiving significantly fewer tokens per dollar but helping projects reach their Raise Target.

## Anti-Manipulation Properties

The continuous pricing model prevents common fundraising manipulation tactics. Front-running is impossible because prices calculate deterministically from on-chain state at execution time—no ability to observe and insert orders ahead of others. Large purchases pay proportional price impact, making whale accumulation at low prices economically unfeasible. Teams cannot grant preferential pricing to insiders—all participants face identical pricing curves determined by mathematical formulas and purchase sequence. Every transaction is verifiable on-chain by querying reserve states and reproducing constant product calculations.

## Continuous Discovery vs Fixed Pricing

Traditional fixed-price sales force teams to guess valuations before knowing demand, creating systematic problems. Overpriced sales fail to raise capital and get cancelled. Underpriced sales complete instantly but leave value on the table for bots and front-runners.

DeLong's continuous discovery eliminates this prediction requirement—final price emerges organically from actual participation patterns. Teams don't guess valuations; the market reveals them through demonstrated demand. This creates fairness through deterministic mathematical rules rather than discretionary decisions. Early supporters receive better terms through timing and conviction, not insider access. Larger purchases pay proportionally higher prices, preventing value extraction by single actors. All execution is transparent and verifiable on-chain, enabling anyone to reproduce calculations and confirm correct allocations.
