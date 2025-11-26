# FAQ

Frequently asked questions about DeLong Protocol.

**What is DeLong Protocol?**

DeLong Protocol provides economic infrastructure for tokenizing revenue-generating datasets through Initial Data Offerings, governance-controlled treasuries, and automated dividend distribution.

**What blockchains does DeLong support?**

Currently deployed on Ethereum mainnet and Sepolia testnet. See [Deployed Contracts](deployed-contracts.md) for addresses.

**Is the protocol audited?**

See [Security](security.md) for audit reports and bug bounty information.

**How does Virtual AMM pricing work?**

Initial price starts at $$0.01$$ and rises continuously as tokens sell, following constant product formula $$K = x \times y$$. Earlier participants receive better pricing.

**What happens if IDO doesn't sell all tokens in 100 days?**

Status changes to Failed. Token holders can call `claimRefund()` to burn tokens and receive proportional USDC refunds.

**Can I sell tokens back during IDO?**

Yes, using `swapExactTokensForUSDC()` or `swapTokensForExactUSDC()` with 0.5% fee. After launch, trade on Uniswap instead.

**How do I create a proposal?**

Hold ≥1% of total supply, then call `createFundingProposal()`, `createPricingProposal()`, `createDelistProposal()`, or `createGovernanceUpgradeProposal()`.

**What's the voting threshold?**

Proposals require >50% of total supply voting FOR. Not just majority of participants—majority of all tokens.

**Can the team withdraw treasury without voting?**

No. Zero admin functions exist. All withdrawals require 7-day voting period + 50% quorum approval.

**How are rental dividends distributed?**

95% of rental payments flow to RentalPool. Token holders claim proportional dividends via `claimDividends()` anytime—no deadlines.

**Do I lose dividends if I transfer tokens?**

No. `_update` hook automatically settles pending dividends to `pendingClaim` before transfers, preserving your earnings.

**How often should I claim?**

No minimum or maximum. Gas costs typically make weekly/monthly claims more economical than daily.

**What decimal precision do contracts use?**

USDC: 6 decimals. Dataset tokens: 18 decimals. Dividend precision: 1e18 scaling factor.

**Can LP tokens ever be unlocked?**

Only via democratic delisting (50% quorum). No admin override, no emergency unlock, no timelock expiry.

**What's the refund rate after delisting?**

40-90% depending on treasury usage. Early abandonment recovers 90%+, complete treasury exhaustion still recovers 50%+ from LP fees.
