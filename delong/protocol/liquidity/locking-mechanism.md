# LP Locking Mechanism

Uniswap LP tokens created at launch are permanently locked in governance custody with no removal mechanism except democratic delisting. This guarantees secondary market liquidity throughout project lifecycle.

## Lock Process

When an IDO completes successfully, the launch function:

1. Calculates LP requirements based on final price and team allocation
2. Creates Uniswap V2 pair if it doesn't exist
3. Adds liquidity: LP tokens × final price USDC
4. Receives LP tokens representing pool ownership
5. Transfers LP tokens to governance contract
6. Governance records LP token address and amount

The LP tokens enter governance custody immediately—no intermediate addresses, no team custody, no delay.

## Permanent Lock

The governance contract holds LP tokens with no transfer mechanism. No functions exist to:
- Transfer LP tokens to external addresses
- Approve external contracts to access LP tokens
- Remove liquidity while project operates normally
- Upgrade contract logic to add removal functions

The only LP token movement path is delisting execution after successful majority vote.

## Guaranteed Liquidity

Locked LP guarantees secondary market trading:

**Traders can always**:
- Buy tokens on Uniswap regardless of project status
- Sell tokens on Uniswap regardless of team actions
- Exit positions without depending on team liquidity

**Teams cannot**:
- Remove liquidity to fund operations
- Drain pool to exit positions
- Reduce liquidity during price decline
- Use LP tokens as collateral

This eliminates the "exit liquidity" problem where teams drain pools after launch, leaving investors unable to trade.

## LP Token Custody

### Storage Location

LP tokens sit in governance contract balance:

$$
\text{lpToken.balanceOf(governance)} = \text{Total LP Tokens}
$$

Anyone can verify this on-chain by querying the LP token contract.

### No Approval Grants

The governance contract never approves external contracts to access LP tokens except during delisting execution. No standing approvals exist that could be exploited.

### No Admin Keys

No admin functions, owner privileges, or special roles exist that could bypass the lock. The contract code physically prevents LP token movement outside delisting.

## Lock Verification

Anyone can verify LP lock security:

**1. Check LP balance**:
Query LP token contract for governance balance—should equal total LP tokens minted at launch.

**2. Read contract code**:
Examine verified governance contract on Etherscan—confirm no transfer functions except delisting execution.

**3. Monitor events**:
Watch for LP token Transfer events from governance address—should be none except delisting.

**4. Verify no approvals**:
Check LP token allowances for governance—should be zero except during active delisting execution.

## Comparison with Traditional Locks

| Feature | DeLong Permanent Lock | Traditional Time Lock | No Lock (Team Controlled) |
|---------|----------------------|---------------------|--------------------------|
| **Duration** | Until delisting vote | Fixed period (6-12 months) | None |
| **Removal path** | Democratic majority vote | Automatic after expiration | Team unilateral |
| **Attack resistance** | Requires 50%+ token acquisition | Wait until unlock | Immediate rugpull |
| **Investor protection** | Maximum | Temporary | None |
| **Team flexibility** | Requires governance approval | Full after unlock | Complete |

Traditional time locks provide temporary protection that expires. DeLong's permanent lock persists until formal project shutdown via democratic process.

## Delisting Exception

The only mechanism to access locked LP tokens is delisting proposal execution:

### Delisting Requirements

1. Token holder creates delisting proposal (requires 1% holdings)
2. Seven-day voting period
3. Majority approval (50%+ of total supply votes yes)
4. Anyone triggers execution
5. Contract removes liquidity from Uniswap
6. Recovered USDC combines with treasury for proportional refunds

Even in delisting, LP tokens don't transfer to team—they're used to recover capital for all token holders proportionally.

## LP Fee Accumulation

While locked, LP tokens accumulate 0.3% trading fees from all Uniswap swaps:

$$
\text{LP Value} = \text{Initial USDC} + \sum \text{Trading Fees}
$$

These accumulated fees enhance delisting refund pools—even failed projects recover more than initial LP allocation due to trading activity.

### Fee Examples

**Scenario 1**: \$1M total trading volume
- Trading fees: \$1M × 0.003 = \$3,000
- LP value increase: \$3,000
- Delisting recovery boost: +6% (on \$50K initial LP)

**Scenario 2**: \$10M total trading volume
- Trading fees: \$10M × 0.003 = \$30,000
- LP value increase: \$30,000
- Delisting recovery boost: +60% (on \$50K initial LP)

High trading volume significantly increases capital recovery potential in failure scenarios.

## Lock as Collateral

The permanent LP lock functions as non-removable collateral guaranteeing exit liquidity:

**Traditional bonds**: Issuer posts collateral, bondholders can claim if default

**DeLong LP lock**: Project posts liquidity, token holders can claim via delisting if failure

This collateralization creates downside protection absent in typical token launches.
