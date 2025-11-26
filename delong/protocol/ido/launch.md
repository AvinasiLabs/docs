# Launch Process

When all salable tokens are sold, the protocol automatically transitions the project from fundraising to operational status. This process creates the permanent Uniswap liquidity pool, deposits treasury funds under governance control, and enables token trading in a single atomic transaction.

## Launch Trigger

Launch triggers automatically when all salable tokens are sold:

$$
\text{soldTokens} = S_{sale}
$$

where $$S_{sale}$$ = $$(1 - \alpha) \times S_{total}$$ represents the tokens allocated for public sale.

The virtual AMM is constructed such that when all salable tokens are purchased, the accumulated USDC (after fees) closely approximates the funding goal $$R_{target}$$. Minor differences may occur due to rounding across multiple transactions, but the protocol uses the actual accumulated USDC balance rather than the theoretical target for capital distribution.

Launch execution is automatic and permissionless—the purchase transaction that sells the final tokens immediately triggers the launch process in the same transaction. The protocol enforces a 100-day fundraising period. Projects that fail to sell all tokens within this period can enter a failed state, enabling refunds to investors.

### Fundraising Timeline

The IDO operates on a fixed 100-day timeline from initialization:

- **Days 0-100**: Active fundraising period where investors can purchase tokens
  - If all tokens are sold during this period, launch triggers automatically
- **Day 100+**: If tokens remain unsold, anyone can trigger the refund process

This design balances two objectives: providing sufficient time for organic fundraising while preventing indefinite capital lock for failed projects. Successful IDOs launch automatically when tokens sell out (potentially well before day 100), while failed IDOs enter refund mode after the deadline.

## Capital Distribution

At launch, the protocol distributes the actual accumulated USDC balance between liquidity pool creation and treasury deposit. The distribution is calculated dynamically based on the final price:

$$
P_{final} = \frac{x_{final}}{y_{final}}
$$

$$
USDC_{LP} = S_{LP} \times P_{final}
$$

$$
USDC_{treasury} = USDC_{balance} - USDC_{LP}
$$

where:
- $$USDC_{balance}$$ = actual accumulated USDC from all sales (after protocol fees)
- $$S_{LP} = \alpha \times S_{total}$$ = tokens reserved for liquidity pairing
- $$P_{final}$$ = price calculated from virtual AMM reserves at launch (when all tokens are sold)
- $$x_{final}$$, $$y_{final}$$ = virtual AMM reserve states at the moment `soldTokens == salableTokens`

## Launch Price Calculation

The final price at which the Uniswap pool launches equals the ratio of USDC to tokens in the LP:

$$
P_{launch} = \frac{USDC_{LP}}{S_{LP}}
$$

This price represents the market-clearing price after all fundraising is complete. Early IDO participants who purchased near $$P_0 = 0.01$$ receive tokens at prices far below $$P_{launch}$$, while the last participants purchase close to launch pricing.

## Permanent Liquidity Guarantee

The Uniswap LP tokens are minted directly to the governance contract with no removal mechanism except democratic delisting (see [liquidity/locking-mechanism.md](../liquidity/locking-mechanism.md) for complete details). This eliminates the exit liquidity problem common in token launches where teams drain pools after trading begins.

## Treasury Custody

Treasury funds deposited to governance are only accessible through successfully passed funding proposals requiring:
- Majority token holder support (50%+ of total supply voting yes)
- Seven-day voting period for community review
- Execution transaction after deadline passes

No emergency withdrawals, admin overrides, or alternative extraction paths exist. Teams must justify expenditures and demonstrate value to maintain funding access.

## Atomic Launch Execution

All launch operations execute in a single transaction. Either the entire process succeeds—pool created, treasury deposited, transfers enabled—or the transaction reverts leaving fundraising status unchanged. The execution sequence includes:

1. **Calculate final state** – Determine final price from virtual + real reserves
2. **Calculate LP requirements** – Compute $$USDC_{LP}$$ and verify sufficiency
3. **Create Uniswap LP** – Pair $$USDC_{LP}$$ with $$S_{LP}$$ tokens, mint LP tokens to governance
4. **Deposit treasury** – Transfer $$USDC_{treasury}$$ to governance internal balance
5. **Enable transfers** – Unfreeze token contract to allow trading
6. **Update phase** – Mark fundraising as completed

This atomicity prevents partial states where liquidity exists without treasury deposits, or where tokens become transferable before trading infrastructure is ready.

## Trading Activation

Token transfers remain frozen during fundraising to prevent premature circulation before liquidity exists. The freeze is enforced at the token contract level—all transfer calls revert except for minting during IDO purchases.

The launch process atomically enables transfers with pool creation, ensuring trading infrastructure exists before tokens become transferable. After launch, token holders can:

- Trade freely on Uniswap at prices determined by supply and demand
- Transfer tokens between addresses without restrictions
- Claim accumulated dividends from rental revenue
- Participate in governance votes on proposals

## Post-Launch Operations

Following successful launch, several new capabilities become available:

**Secondary market trading** – Token holders buy and sell on Uniswap at prices determined by constant product dynamics. The permanently locked LP guarantees liquidity depth.

**Dividend distribution** – Rental revenue from researchers accessing datasets flows to token holders proportionally. The accumulated rewards algorithm enables O(1) claiming without per-holder updates.

**Governance participation** – Token holders create and vote on funding requests, pricing adjustments, and project continuation decisions. All proposals require majority approval.

**Operational funding** – Project teams request treasury withdrawals through detailed funding proposals. Token holders evaluate budgets and approve legitimate operational expenses.

**Dataset access** – Researchers pay rental fees to access datasets in TEE environments. Revenue splits 95% to dividend pool, 5% to protocol fees.

## Launch Verification

Anyone can verify launch executed correctly by querying on-chain state:

**LP creation verification:**
1. Query Uniswap Factory for pair address: `factory.getPair(datasetToken, USDC)`
2. Check LP token balance: `lpToken.balanceOf(governance)` equals total LP token supply
3. Verify pool reserves match expected $$USDC_{LP}$$ and $$S_{LP}$$ values
4. Confirm LP tokens have no alternative holders

**Treasury deposit verification:**
1. Query governance treasury balance
2. Verify equals expected $$USDC_{treasury} = R_{target} - USDC_{LP}$$
3. Check no withdrawal proposals have executed yet
4. Confirm treasury can only decrease via proposal execution

**Transfer status verification:**
1. Query token contract freeze status
2. Attempt transfer transaction (should succeed post-launch)
3. Check token balances update correctly
4. Verify Uniswap swaps execute successfully

## Failed Launches

Projects that fail to reach their funding goal within the 100-day period enter a failed state. After the deadline expires, anyone can trigger the refund process, which enables proportional capital recovery for all token holders.

### Refund Mechanism

When an IDO fails, token holders can claim refunds based on their token holdings:

$$
\text{Refund Amount} = \frac{\text{User Token Balance} \times \text{Total USDC Raised}}{\text{Total Tokens Sold}}
$$

The refund rate equals the average price paid across all purchases. Early investors who bought at lower prices receive refunds at this average price, while late investors who bought at higher prices may receive less than they paid. This socializes the cost of failure proportionally.

### Claiming Refunds

To claim a refund, token holders:
1. Wait for someone to trigger the failed state (after day 100 if goal not reached)
2. Call the refund claim function with their tokens
3. Receive USDC proportional to their token holdings
4. Tokens are burned or locked, removing them from circulation

During the active fundraising period (before failure), investors can exit by selling tokens back to the virtual AMM at current prices (minus 0.5% fee), though this typically results in worse pricing than waiting for proportional refunds.

## Launch as Investor Protection

The atomic launch process removes trust requirements from several critical transitions:

**Liquidity guarantee** – LP tokens mint directly to governance with no team custody risk. Exit liquidity is mathematically guaranteed by the permanent lock.

**Treasury custody** – Funds enter governance control immediately with no intermediate addresses. Teams cannot access capital without community approval.

**Trading fairness** – Transfers enable only when liquidity exists, preventing situations where insiders trade while public faces no liquidity.

**Deterministic execution** – All operations follow mathematical rules without discretionary decisions. Teams cannot manipulate launch pricing, LP sizing, or treasury splits.

The launch process establishes the foundation for secure, governance-controlled operations that protect investor interests throughout the project lifecycle.
