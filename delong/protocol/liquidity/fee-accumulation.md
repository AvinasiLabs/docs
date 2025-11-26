# Trading Fee Accumulation

Uniswap charges 0.3% fees on all trades, accumulating in the LP position over time. DeLong's locked LP captures these fees, enhancing value for delisting refunds even as projects fail.

## Uniswap Fee Mechanism

Every swap through Uniswap pays a 0.3% fee added to the liquidity pool:

**Trader action**: Swap 1,000 USDC for tokens
**Fee deduction**: 3 USDC (0.3%)
**Actual swap**: 997 USDC for tokens
**Fee destination**: Added to pool USDC balance

The fee doesn't transfer out—it increases pool reserves, raising LP token value.

## Fee Accumulation Formula

LP value grows with trading volume:

$$
\text{LP Value}_{t} = \text{LP Value}_0 + \sum_{i=1}^{t} (\text{Volume}_i \times 0.003)
$$

Where:
- $\text{LP Value}_0$ = Initial value at launch
- $\text{Volume}_i$ = Trading volume in period $i$
- $0.003$ = 0.3% fee rate

### Example Accumulation

**Initial LP**: \$25,000 USDC + 625,000 tokens at \$0.04

**Month 1**: \$500,000 volume
- Fees: \$500,000 × 0.003 = \$1,500
- LP value: \$25,000 + \$1,500 = \$26,500 (+6%)

**Month 2**: \$800,000 volume
- Fees: \$800,000 × 0.003 = \$2,400
- LP value: \$26,500 + \$2,400 = \$28,900 (+15.6% total)

**Month 3**: \$1,200,000 volume
- Fees: \$1,200,000 × 0.003 = \$3,600
- LP value: \$28,900 + \$3,600 = \$32,500 (+30% total)

After 3 months, \$2.5M cumulative volume generated \$7,500 in fees, increasing LP value by 30%.

## Volume Drivers

Trading volume depends on several factors:

**Price volatility** – Large price swings incentivize trading, generating higher volume

**Speculation** – Active traders buying dips and selling rallies create consistent volume

**Market making** – Bots arbitraging price differences between venues generate volume

**Liquidity depth** – Deeper pools support larger trades without excessive slippage

**Secondary market interest** – More investors trading = more volume

## Fee Distribution

Fees don't distribute separately—they're embedded in LP value:

**Not distributed**:
- No periodic fee claims for LP holders
- No dividend-style payments
- No separate fee tracking per LP token

**Embedded in value**:
- LP tokens represent proportional pool ownership
- Pool value includes accumulated fees
- Redeeming LP tokens captures proportional share

For DeLong's locked LP, fees remain embedded until delisting triggers LP redemption.

## Fees in Delisting

When projects delist, LP removal recovers base capital plus accumulated fees:

$$
\text{Recovered USDC} = \text{Base USDC} + \text{Total Fees} \pm \text{IL Effects}
$$

### Recovery Scenarios

**Scenario 1**: Moderate volume, stable price
- Initial LP: \$50,000
- Trading fees: \$8,000 (from \$2.67M volume)
- IL: Minimal
- Recovery: \$58,000 (116%)

**Scenario 2**: High volume, price increase
- Initial LP: \$50,000
- Trading fees: \$20,000 (from \$6.67M volume)
- IL: -\$10,000 (price moved)
- Recovery: \$60,000 (120%)

**Scenario 3**: Low volume, stable price
- Initial LP: \$50,000
- Trading fees: \$1,500 (from \$500K volume)
- IL: Minimal
- Recovery: \$51,500 (103%)

Even modest trading activity improves delisting refunds beyond initial LP allocation.

## Volume Projections

Projects can estimate fee accumulation from expected trading patterns:

**Conservative** (daily volume = 2% of market cap):
- Market cap: \$100,000
- Daily volume: \$2,000
- Monthly volume: \$60,000
- Monthly fees: \$180
- Annual fees: \$2,160

**Moderate** (daily volume = 5% of market cap):
- Market cap: \$100,000
- Daily volume: \$5,000
- Monthly volume: \$150,000
- Monthly fees: \$450
- Annual fees: \$5,400

**High** (daily volume = 10% of market cap):
- Market cap: \$100,000
- Daily volume: \$10,000
- Monthly volume: \$300,000
- Monthly fees: \$900
- Annual fees: \$10,800

Higher speculation and volatility drive volume toward the high end.

## Fee APR Calculation

Traditional LPs measure returns as annual percentage rate:

$$
\text{Fee APR} = \frac{\text{Annual Fees}}{\text{LP Value}} \times 100\%
$$

For DeLong locked LP:

**Example calculation** (\$25K LP, \$3K annual fees):
$$
\text{Fee APR} = \frac{3,000}{25,000} \times 100\% = 12\%
$$

This represents value accrual to the locked position, distributed across all token holders proportionally.

## Comparison to Rental Revenue

Projects receive income from two sources:

**Trading fees**: Embedded in LP, only accessible via delisting

**Rental revenue**: 95% flows to dividend pool, claimable anytime

|Aspect | Trading Fees | Rental Revenue |
|---------|--------------|----------------|
| **Rate** | 0.3% per trade | Set by governance (e.g., \$10/hour) |
| **Volume** | Secondary market trading | Researcher access purchases |
| **Distribution** | Embedded in LP value | Direct dividend claims |
| **Accessibility** | Delisting only | Immediate |
| **Predictability** | Varies with speculation | Depends on dataset demand |

Trading fees are a bonus that enhances delisting refunds, while rental revenue is the primary income source for token holders.

## Fee Capture Efficiency

Locked LP captures 100% of trading fees on its proportional pool share:

**Pool composition** at launch:
- Locked LP: 100% of liquidity
- Fee capture: 100% of fees

Unlike protocols where LPs compete for fees across multiple pools, DeLong's single locked LP captures all fees on its pair, maximizing value accumulation.
