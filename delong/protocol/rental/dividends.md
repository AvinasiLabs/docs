# Dividend Distribution

The Accumulated Rewards algorithm enables automatic dividend distribution to all token holders with O(1) gas complexity, regardless of holder count. Revenue from dataset rentals flows to token holders proportionally without manual claims or batch processing.

## The Distribution Problem

Traditional dividend distribution requires iterating through all holders to calculate and send payments—impossible at scale due to blockchain gas limits. A project with 10,000 holders would exceed block gas limits attempting direct distribution.

## Accumulated Rewards Algorithm

The algorithm inverts the problem: instead of updating every holder when revenue arrives, update a single global accumulator. Holders calculate owed amounts on-demand when they interact with the contract.

### Global Accumulator

$$
\text{accRevenuePerToken} = \sum_{i=1}^{n} \frac{\text{Revenue}_i}{\text{TotalSupply}_i}
$$

This tracks cumulative revenue per token across all revenue deposits since contract deployment.

### Revenue Notification

When rental revenue arrives, the IDO contract notifies the dividend pool:

$$
\text{accRevenuePerToken} \mathrel{+}= \frac{\text{New Revenue} \times 10^{18}}{\text{Total Supply}}
$$

Gas cost: ~5,000 gas regardless of holder count.

### Reward Debt Tracking

Each holder has a debt value tracking revenue already accounted:

$$
\text{rewardDebt}[user] = \text{balance}[user] \times \text{accRevenuePerToken at last update}
$$

### Pending Rewards Calculation

At any time, a holder's claimable dividends equal:

$$
\text{Pending} = \frac{\text{balance} \times \text{accRevenuePerToken}}{10^{18}} - \text{rewardDebt}[user]
$$

This calculates the difference between:
- Total revenue attributable to current balance
- Revenue already accounted in previous claims/transfers

### Claim Process

When a holder claims dividends:

1. Calculate pending: `pending = (balance × accRevenuePerToken / 1e18) - rewardDebt`
2. Transfer USDC: `transfer(user, pending)`
3. Update debt: `rewardDebt[user] = balance × accRevenuePerToken / 1e18`

Gas cost: ~40,000 gas regardless of revenue history.

### Transfer Integration

Token transfers must update reward debt for both sender and recipient to maintain accurate accounting:

**Sender**: Claim pending rewards, update debt to zero out balance
**Recipient**: Update debt to account for new balance at current accumulator

This ensures dividends follow token ownership correctly.

## Example Walkthrough

**Initial state**:
- Total supply: 1,000,000 tokens
- Alice holds: 100,000 tokens (10%)
- accRevenuePerToken: 0

**Revenue 1**: \$5,000 arrives
- accRevenuePerToken: 0 + (5,000 × 1e18 / 1,000,000) = 5,000,000,000,000,000
- Alice's pending: (100,000 × 5,000,000,000,000,000 / 1e18) - 0 = \$500

**Alice claims**:
- Transfer \$500 to Alice
- rewardDebt[Alice] = 100,000 × 5,000,000,000,000,000 / 1e18 = \$500

**Revenue 2**: \$3,000 arrives
- accRevenuePerToken: 5,000,000,000,000,000 + (3,000 × 1e18 / 1,000,000) = 8,000,000,000,000,000
- Alice's pending: (100,000 × 8,000,000,000,000,000 / 1e18) - 500 = \$300

The algorithm correctly calculates \$300 new dividends for Alice despite her \$500 previous claim.

## Gas Efficiency

| Operation | Gas Cost | Scales With |
|-----------|----------|-------------|
| Revenue notification | ~5,000 | Constant |
| Claim dividends | ~40,000 | Constant |
| Transfer (with dividend update) | ~55,000 | Constant |

Compare to naive distribution: 1,000 holders = ~20M gas, exceeding most block limits.

## Precision Handling

The algorithm multiplies by $$10^{18}$$ to maintain precision with integer arithmetic:

$$
\text{accRevenuePerToken} = \frac{\text{Revenue} \times 10^{18}}{\text{Total Supply}}
$$

This allows accurate per-token accounting down to 18 decimal places, sufficient for USDC (6 decimals).

### Rounding Considerations

Small amounts of revenue may round down when divided across large supplies:

- Revenue: \$1
- Supply: 10,000,000 tokens
- Per token: \$0.0000001
- With 18 decimals: 100,000,000,000 wei per token

Rounding errors are negligible—maximum loss per holder is less than 0.000001 USDC.

## Automatic Distribution

Revenue flows automatically from rental payments to dividend pool:

1. Researcher pays rental fee to IDO contract
2. IDO calculates split: 95% dividends, 5% protocol fee
3. IDO transfers dividend portion to rental pool
4. Rental pool calls notifyRevenue to update accumulator
5. Token holders can now claim increased pending amounts

No manual intervention, batch processing, or periodic settlements required.

## Unclaimed Dividends

Dividends remain claimable indefinitely. No expiration exists—holders can claim months or years after revenue arrives.

Unclaimed amounts stay in the rental pool contract as USDC balance. The contract tracks pending amounts via the reward debt calculation, not by holding separate balances per user.

## Battle-Tested Design

This algorithm powers dividend distribution in:
- Uniswap V2 LP reward farming
- SushiSwap MasterChef contracts
- Compound COMP distribution
- Dozens of other major DeFi protocols

Multi-billion dollar systems rely on this math for accurate, gas-efficient reward tracking.
