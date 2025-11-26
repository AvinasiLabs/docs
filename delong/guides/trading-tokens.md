# Trading Tokens

Guide for secondary market trading on Uniswap after IDO launch.

## Launch Transition

When IDO completes (all salable tokens sold), automatic launch:

1. Creates Uniswap V2 pair for Dataset Token / USDC
2. Adds liquidity using LP allocation formula
3. Locks LP tokens permanently in Governance
4. Unfreezes token transfers via `DatasetToken.unfreeze()`
5. Deposits remaining USDC + project tokens to Governance treasury

**Launch Price**:
$$
P_{launch} = \frac{USDC_{LP}}{S_{LP}}
$$

Typically 4x higher than $0.01 initial price, rewarding early IDO participants.

## Trading on Uniswap

**Finding the Pool**:

Query `IDO.liquidityPair` for Uniswap V2 pair address, or search Uniswap interface for dataset token symbol.

**Standard Uniswap Operations**:
- Swap USDC for dataset tokens
- Swap dataset tokens for USDC
- No fees besides standard Uniswap 0.3% trading fee

**No Liquidity Removal**: LP tokens locked permanently in Governance. Only path to unlock is democratic delisting (50% quorum).

## Liquidity Dynamics

**Initial Liquidity**: Determined by alpha parameter and actual USDC raised

**Growing Liquidity**: LP earns 0.3% fees from all trades, increasing pool depth over time

**Price Impact**: Smaller pools have higher slippage. Check Uniswap quotes before large trades.

**Arbitrage**: Price differences between IDO (if still active) and Uniswap enable arbitrage until IDO launches.

## Transfer Mechanics

**Dividend Settlement**:

All transfers trigger automatic dividend settlement via `_update` hooks:

1. Before transfer: Settle sender's pending dividends to `pendingClaim`
2. Execute balance update
3. After transfer: Update sender and receiver `rewardDebt` baselines

**Result**: No dividend loss during transfers. Claim anytime via `RentalPool.claimDividends()`.

**Gas Costs**: Transfers cost more gas than standard ERC-20 due to dividend settlement hooks (~150k gas vs ~65k gas).

## Token Holder Benefits

**Dividend Rights**:
- Receive proportional share of 95% rental revenue
- Pull-based claiming (no automatic distributions)
- No claim deadlines

**Governance Rights**:
- Vote on treasury withdrawals
- Vote on rental pricing changes
- Vote on delisting and governance upgrades
- Create proposals if holding ≥1% supply

**Capital Recovery**:
- Trade on Uniswap for immediate exit
- Or vote to delist for proportional capital recovery (40-90% depending on treasury usage)

## Price Discovery

**Factors Affecting Price**:

**Rental Revenue** - Higher revenue → higher dividend yields → price appreciation

**Treasury Usage** - Conservative spending → larger capital base → price support

**Governance Quality** - Strong proposals and execution → community confidence → price support

**Liquidity** - More LP fees → deeper pools → lower slippage → better trading experience

**Market Sentiment** - Longevity research hype, dataset utility, competitive landscape

**Supply/Demand** - Fixed supply, demand from researchers and dividend seekers

## Trading Strategies

**Long-Term Holding**:
- Focus on dividend yield from rental revenue
- Participate in governance to protect investment
- Accumulate during low-utilization periods

**Dividend Farming**:
- Buy before high-utilization periods
- Claim dividends regularly
- Sell after rental demand peaks

**Arbitrage**:
- Monitor IDO pricing vs Uniswap during fundraising
- Front-run large IDO purchases (if profitable vs gas costs)
- Close spreads for risk-free profits

**Governance Plays**:
- Accumulate to 1% threshold for proposal rights
- Build voting coalitions for specific outcomes
- Exit before controversial proposals

## Risk Management

**Liquidity Risk**: Locked LP guarantees pool exists but not trading volume. Low-volume pools have high slippage.

**Governance Risk**: 50% majority can withdraw treasury or delist project. Monitor proposals carefully.

**Revenue Risk**: Dividend yield depends on dataset adoption. Low utilization = low yields.

**Smart Contract Risk**: Protocol not yet audited. See [security](../resources/security.md) for audit status.

**Impermanent Loss**: Not applicable to regular traders. Only affects locked LP tokens in Governance (which can't be unlocked except via delisting).

## Tax Considerations

**Disclaimer**: Not tax advice. Consult tax professional.

**Potential Taxable Events**:
- Purchasing tokens during IDO (cost basis = USDC spent)
- Trading on Uniswap (capital gains/losses)
- Claiming dividends (ordinary income or dividends, jurisdiction-dependent)
- Receiving governance vote rewards (if implemented)

**Record Keeping**:
- Save transaction hashes for all purchases, sales, and claims
- Track cost basis per tax lot (FIFO, LIFO, or specific identification)
- Document rental revenue claims with amounts and dates

## Monitoring Tools

**On-Chain Data**:
- `IDO.getProgress()` - Fundraising status
- `RentalPool.getPendingDividends(user)` - Claimable amount
- `Governance.proposals(id)` - Proposal details
- Uniswap pool reserves and price

**Events to Track**:
- `TokensPurchased` / `TokensSold` - IDO activity
- `AccessPurchased` - Rental demand
- `RevenueAdded` - Dividend pool growth
- `ProposalCreated` / `VoteCast` - Governance activity

**Price Charts**: Use Uniswap Analytics or Dextools for price history and volume

See [resources/faq.md](../resources/faq.md) for common trading questions.
