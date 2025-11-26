# IDO Contract

The IDO contract implements Initial Data Offering with Virtual AMM pricing.

## Core State

**Configuration parameters:**
- `rTarget` - Funding goal in USDC (6 decimals)
- `alpha` - Project ownership ratio in basis points (1-5000 = 0.01%-50%)
- `projectAddress` - Project owner multisig wallet
- `tokenAddress` - Dataset token contract
- `governance` - Governance contract (integrated treasury + voting)
- `rentalPool` - RentalPool for dividend distribution
- `feeTo` - Protocol fee recipient

**Virtual AMM reserves:**
- `reserves.xVirtual` - Virtual USDC balance
- `reserves.yVirtual` - Virtual token balance

**Dynamic state:**
- `soldTokens` - Currently sold tokens
- `usdcBalance` - Actual raised USDC (excluding fees)
- `status` - Active, Launched, or Failed
- `salableTokens` - Total tokens available for sale ((1-alpha) × totalSupply)
- `projectTokens` - Team allocation (alpha × totalSupply)

## Constants

**Fundraising period**: 100 days (`SALE_DURATION`)

**Trading fees:**
- Buy: 0.3% (`BUY_FEE_RATE = 30` basis points)
- Sell: 0.5% (`SELL_FEE_RATE = 50` basis points)

**Rental fees:**
- Protocol: 5% (`PROTOCOL_FEE_RATE = 500` basis points)
- Dividends: 95% to RentalPool

## Initialization

The Factory calls `initialize` after cloning, setting:
- IDO parameters (rTarget, alpha)
- Contract addresses (token, USDC, governance, pool, Uniswap router/factory)
- Metadata URI and hourly rental rate
- Decimal configuration (USDC: 6 decimals, Token: 18 decimals)

The function calculates virtual reserves using VirtualAMM library, determines salableTokens and projectTokens, sets start/end times, and stores initial metadata version.

## Token Trading

### Buying Tokens

**swapUSDCForExactTokens(tokenAmountOut, maxUSDCIn, deadline)** - Buy exact token amount, spending at most maxUSDCIn.

**swapExactUSDCForTokens(usdcAmountIn, minTokensOut, deadline)** - Spend exact USDC amount, receiving at least minTokensOut.

Both charge 0.3% fee, verify slippage protection, transfer USDC from buyer, mint tokens to buyer, and update virtual reserves. If purchase brings `soldTokens == salableTokens`, the contract automatically triggers launch.

### Selling Tokens

**swapExactTokensForUSDC(tokenAmountIn, minUSDCOut, deadline)** - Sell exact token amount, receiving at least minUSDCOut.

**swapTokensForExactUSDC(usdcAmountOut, maxTokensIn, deadline)** - Get exact USDC amount, selling at most maxTokensIn.

Both charge 0.5% fee, verify slippage protection, burn tokens from seller, and transfer USDC refund. Selling is only available during fundraising—after launch, users trade on Uniswap secondary market.

## Pricing Formula

The contract uses Uniswap V2-style constant product:

$$
K = x \times y
$$

For a buy of $\Delta y$ tokens:

$$
\text{Cost} = \frac{x \cdot \Delta y}{y - \Delta y}
$$

For a sell of $\Delta y$ tokens:

$$
\text{Refund} = \frac{x \cdot \Delta y}{y + \Delta y}
$$

Fees are added to cost (buy) or subtracted from refund (sell). Protocol fees flow to `feeTo`, remaining USDC stays in contract as `usdcBalance`.

## Launch Mechanism

Launch triggers when `soldTokens == salableTokens` (all salable tokens sold). The `_launch` function:

1. Sets status to Launched
2. Records launch timestamp and final price
3. Calculates final price from virtual reserves
4. Determines LP allocation using project's actual `usdcBalance` (not rTarget)
5. Creates Uniswap V2 pair if needed
6. Adds liquidity: $USDC_{LP} = S_{LP} \times P_{final}$ and corresponding tokens
7. Locks LP tokens in Governance
8. Transfers remaining USDC and project tokens to Governance treasury
9. Unfreezes token transfers via `DatasetToken.unfreeze()`

The split between LP and treasury uses actual raised amount, which may differ slightly from rTarget due to rounding across multiple transactions.

## Failure Mechanism

If 100 days pass without selling all tokens, anyone can call `triggerRefund()` to mark IDO as Failed. The function calculates refund rate:

$$
\text{Refund Rate} = \frac{\text{usdcBalance}}{\text{soldTokens}}
$$

Token holders then call `claimRefund()` to burn their tokens and receive proportional USDC refund.

## Metadata Management

Project owners call `updateMetadata(newMetadataURI)` to update dataset description. The contract appends to `metadataHistory` array, maintaining complete version history with timestamps.

## Rental System

Researchers call `purchaseAccess(hoursCount, deadline)` to pay for dataset access. The contract:

1. Calculates cost: `hoursCount × hourlyRate`
2. Transfers USDC from researcher
3. Splits payment: 5% to protocol (`feeTo`), 95% to `rentalPool`
4. Extends access expiration: `accessExpiresAt[user] += hoursCount × 1 hour`
5. Calls `RentalPool.addRevenue()` to update dividend accumulator

Token holders call `Governance.createPricingProposal(newRate)` to adjust hourly rates via voting.

## Query Functions

**Price quotes:**
- `getCurrentPrice()` - Returns current token price in USDC
- `predictUSDCIn(tokenAmountOut)` - USDC cost for buying exact tokens (including 0.3% fee)
- `predictUSDCOut(tokenAmountIn)` - USDC received for selling exact tokens (after 0.5% fee)
- `predictTokensOut(usdcAmountIn)` - Tokens received for exact USDC (after 0.3% fee)
- `predictTokensIn(usdcAmountOut)` - Tokens needed to get exact USDC (including 0.5% fee)

**Progress tracking:**
- `getProgress()` - Returns sold, target, percentage, timeLeft
- `getUserInfo(user)` - Returns user's tokens, USDC spent, refund status

**Access verification:**
- `accessExpiresAt[user]` - Timestamp when access expires

**Metadata:**
- `getMetadataHistory()` - Returns all historical metadata versions

## Security Features

**Slippage protection** - maxUSDCIn/minTokensOut/maxTokensIn/minUSDCOut parameters prevent frontrunning attacks during trading.

**Deadline enforcement** - Transaction must execute before deadline timestamp.

**Initialization guard** - `_initialized` flag prevents re-initialization.

**Reentrancy protection** - ReentrancyGuard on all state-changing functions.

**Frozen transfers** - Token transfers restricted during fundraising (exempt: IDO, Uniswap pool after launch).

## Events

Key events for indexing:
- `TokensPurchased` / `TokensSold` - Trading activity
- `IDOLaunched` - Successful launch with LP/treasury amounts
- `IDOFailed` - Fundraise failure
- `AccessPurchased` - Rental payments
- `RentalDistributed` - Revenue distribution to pool
- `MetadataUpdated` - Dataset description changes

Off-chain systems monitor these events to build dataset catalogs, track fundraising progress, and index rental revenue.
