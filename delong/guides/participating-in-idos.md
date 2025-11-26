# Participating in IDOs

Guide for purchasing tokens during Initial Data Offerings.

## Understanding IDO Pricing

DeLong IDOs use Virtual AMM with continuous price discovery starting at $0.01. Price rises as tokens sell following constant product formula $K = x \times y$.

**Early vs Late Entry**:
- First 10% of raise: Near $0.01 pricing, maximum tokens per USDC
- Middle 50%: Moderately higher prices, favorable terms
- Final 40%: Close to launch pricing, fewer tokens per USDC

## Purchasing Tokens

**Available Functions**:

`swapUSDCForExactTokens(tokenAmountOut, maxUSDCIn, deadline)` - Buy exact token amount

`swapExactUSDCForTokens(usdcAmountIn, minTokensOut, deadline)` - Spend exact USDC amount

**Requirements**:
1. Approve USDC spending for IDO contract
2. Set slippage protection limits (maxUSDCIn or minTokensOut)
3. Specify deadline timestamp (typically 20 minutes from now)

## Evaluating Opportunities

**Key Metrics**:
- **rTarget**: Fundraising goal determines total supply
- **Alpha**: Team allocation percentage (lower = more public tokens)
- **Rental Rate**: Initial hourly pricing affects revenue potential
- **Metadata**: Dataset description, use cases, target market

**Progress Tracking**:
- Call `getProgress()` to see sold/target tokens and time remaining
- Monitor `TokensPurchased` events for trading activity
- Check `getCurrentPrice()` for current pricing

## Selling Before Launch

During Active status, use:

`swapExactTokensForUSDC(tokenAmountIn, minUSDCOut, deadline)` - Sell exact tokens

`swapTokensForExactUSDC(usdcAmountOut, maxTokensIn, deadline)` - Get exact USDC

0.5% sell fee applies. After launch, trade on Uniswap instead.

## After Launch

Once `soldTokens == salableTokens`, IDO automatically launches:
1. Creates Uniswap V2 pair
2. Locks LP tokens in Governance
3. Unfreezes token transfers
4. Deposits treasury to Governance

Trade on Uniswap secondary market. Claim rental dividends via RentalPool.

## Risk Considerations

**Price Risk**: Later purchases pay higher prices for same capital

**Liquidity Risk**: Locked LP guarantees secondary market but trading volume depends on community

**Governance Risk**: 50% quorum enables majority to withdraw treasury

**Revenue Risk**: Dividend yield depends on dataset rental adoption

See [FAQ](../resources/faq.md) for common questions.
