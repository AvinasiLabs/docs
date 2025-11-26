# LP Locking Mechanism

Uniswap LP tokens created at launch are permanently locked in the Governance contract with no removal mechanism except democratic delisting. This guarantees secondary market liquidity throughout project lifecycle and prevents rug pulls.

## Why Permanent Locking

**The problem**: In typical token launches, teams control liquidity pools and can drain them after raising capital, leaving investors unable to trade.

**DeLong's solution**: LP tokens are minted directly to the Governance contract and cannot be transferred out except through delisting votes. This creates three protections:

- **Guaranteed exit liquidity** - Investors can always trade on Uniswap regardless of team actions
- **Rug pull prevention** - Teams cannot remove liquidity to exit positions
- **Capital recovery option** - Failed projects can reclaim LP value through democratic delisting

## How Locking Works

When an IDO completes successfully:

1. Protocol calculates LP requirements based on Initial Equity Ratio
2. Creates Uniswap V2 pool (or uses existing)
3. Adds liquidity: tokens + USDC
4. LP tokens are minted directly to Governance contract
5. No transfer functions exist except delisting execution

The Governance contract holds LP tokens with **no** ability to:
- Transfer LP tokens to external addresses
- Approve external contracts to access LP tokens
- Remove liquidity during normal operations
- Upgrade contract to add removal functions

## Lock as Investor Protection

| Feature | DeLong Permanent Lock | Traditional Time Lock | No Lock |
|---------|----------------------|---------------------|---------|
| **Duration** | Until delisting vote | 6-12 months | None |
| **Removal** | Requires 50%+ vote | Automatic expiry | Team unilateral |
| **Protection** | Permanent | Temporary | None |

Traditional time locks expire. DeLong's lock persists until formal shutdown via majority vote.

## Verification Methods

Anyone can verify LP tokens are locked:

- **Check LP balance** - Query LP token contract for Governance balance
- **Read contract code** - Verify no transfer functions exist except delisting
- **Monitor events** - Watch for suspicious LP token movements
- **Verify no approvals** - Confirm governance has no standing approvals

See [Verification Methods](verification.md) for detailed steps.

## Delisting: The Only Exception

The only way to access locked LP tokens is through delisting:

1. Token holder creates delisting proposal (requires 1% of supply)
2. Seven-day voting period
3. Majority approval (50%+ votes yes)
4. Execution removes LP from Uniswap
5. Recovered USDC + treasury distributed proportionally to all holders

Even during delisting, LP tokens don't transfer to team—they're burned to recover capital for all investors.

## Additional Benefits

**Trading fee accumulation**: While locked, the LP position accumulates Uniswap's 0.3% trading fees, increasing LP value over time. This enhances capital recovery during delisting.

**Impermanent loss**: Price changes cause impermanent loss like any LP position, but this only matters if delisting occurs. During normal operation, locked LP simply provides guaranteed trading liquidity.
