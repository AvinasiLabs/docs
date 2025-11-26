# RentalPool Contract

_Author: Dylan, Avinasi Labs_

The RentalPool implements the Accumulated Rewards algorithm with automatic settlement through DatasetToken transfer hooks.

## State Variables

**Token references:**
- `usdc` - USDC token for dividend payments
- `datasetToken` - Associated dataset token
- `idoContract` - Only authorized caller for `addRevenue`

**Precision:**
- `PRECISION = 1e18` - Fixed-point arithmetic scaling factor

**Accounting:**
- Maps tracking per-user debt, pending claims, and claim history

## Initialization

The Factory calls `initialize` after cloning with:
- USDC address
- Dataset token address
- Initial owner (Factory)
- IDO contract address (authorized revenue source)

## Revenue Addition

Only IDO contract calls `addRevenue(amount)` when researchers purchase dataset access (95% of payment after 5% protocol fee).

Algorithm:
1. Get current token total supply
2. Calculate revenue per token: `revenuePerToken = (amount × PRECISION) / totalSupply`
3. Update accumulator: `accRevenuePerToken += revenuePerToken`
4. Increment `totalRevenue += amount`
5. Transfer USDC from IDO to RentalPool

This O(1) operation updates global state regardless of holder count.

## Dividend Settlement

The DatasetToken calls settlement hooks during transfers:

### Before Balance Change

`beforeBalanceChange(user, oldBalance)` - Called before updating balances.

Algorithm:
1. Calculate pending: `pending = (oldBalance × accRevenuePerToken) / PRECISION - rewardDebt[user]`
2. Add to pending claim: `pendingClaim[user] += pending`

This settles dividends earned so far based on old balance and adds to claimable amount.

### After Balance Change

`afterBalanceChange(user, newBalance)` - Called after updating balances.

Algorithm:
1. Update baseline: `rewardDebt[user] = (newBalance × accRevenuePerToken) / PRECISION`

This resets the debt baseline to new balance, preventing double-claiming of future rewards.

## Claiming Dividends

Users call `claimDividends()` to receive accumulated USDC.

Algorithm:
1. Get current balance
2. Calculate new pending: `newPending = (balance × accRevenuePerToken) / PRECISION - rewardDebt[user]`
3. Combine with stored: `totalPending = pendingClaim[user] + newPending`
4. Verify sufficient contract USDC balance
5. Transfer totalPending USDC to user
6. Reset `pendingClaim[user] = 0`
7. Update `rewardDebt[user] = (balance × accRevenuePerToken) / PRECISION`
8. Increment `totalClaimed += totalPending`
9. Increment `userTotalClaimed[user] += totalPending`

Users pay their own gas for claiming—RentalPool never pushes payments.

## Query Functions

**getPendingDividends(user)** - View unclaimed dividends:

$$
\text{Pending} = \text{pendingClaim}[user] + \frac{\text{balance} \times \text{accRevenuePerToken}}{10^{18}} - \text{rewardDebt}[user]
$$

**getUserInfo(user)** - Returns:
- Current balance
- Pending dividends
- Total claimed historically
- Reward debt baseline

**totalRevenue** / **totalClaimed** - Protocol-wide statistics.

## Transfer Safety

Without settlement hooks, transferring tokens would forfeit dividends:

**Bad scenario (no hooks):**
1. Alice has 100 tokens, 50 USDC pending
2. Alice transfers 100 tokens to Bob
3. Alice's balance becomes 0, can no longer claim 50 USDC
4. Bob's debt baseline set to current accumulator
5. Alice loses 50 USDC permanently

**With hooks:**
1. Before transfer: settle Alice's 50 USDC to `pendingClaim[Alice]`
2. After transfer: update both Alice and Bob's debt baselines
3. Alice can claim 50 USDC even with 0 balance
4. Bob's debt correctly reflects receiving tokens with no prior dividends

The two-phase hook ensures fair dividend accounting regardless of transfer patterns.

## Authorization

**Only IDO can add revenue** - `addRevenue` reverts if `msg.sender != idoContract`. This prevents unauthorized inflation of dividend pool.

**Only DatasetToken can call hooks** - `beforeBalanceChange` and `afterBalanceChange` revert if `msg.sender != datasetToken`. This prevents manipulation of dividend accounting.

**Anyone can claim** - Users claim their own dividends permissionlessly.

## Security Features

**Initialization guard** - `_initialized` flag prevents re-initialization.

**Reentrancy protection** - ReentrancyGuard on `claimDividends` prevents reentrancy attacks.

**Overflow protection** - Uses 1e18 precision scaling to prevent rounding errors while maintaining accuracy.

**Balance verification** - Claims revert if contract lacks sufficient USDC balance.

**No admin functions** - No privileged roles can withdraw USDC or modify accounting.

## Precision and Rounding

The 1e18 precision multiplier enables accurate per-token accounting even with small amounts:

**Example:**
- Total supply: 1,000,000 tokens (1e24 smallest units)
- Revenue: 100 USDC (100e6 smallest units)
- Revenue per token: (100e6 × 1e18) / 1e24 = 1e2 = 100

A holder with 1 token (1e18 units) receives:
- Pending: (1e18 × 100) / 1e18 = 100 smallest USDC units = 0.0001 USDC

This precision prevents dust loss while maintaining fairness across all holder sizes.

## Events

**RevenueAdded(amount, accRevenuePerToken)** - Emitted when IDO adds revenue.

**DividendsClaimed(user, amount)** - Emitted when user claims dividends.

Off-chain indexers monitor these to track revenue flow and claim history.

## Gas Efficiency

**Adding revenue**: O(1) - Single storage update regardless of holder count.

**Claiming**: O(1) - User-specific calculation, no iteration.

**Transfers**: O(1) - Fixed two hook calls per transfer.

This scales to thousands of token holders without degrading performance, unlike naive iteration-based distribution.
