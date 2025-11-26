# Impermanent Loss

Impermanent loss affects all Uniswap LP positions when token prices diverge from initial ratios. DeLong's permanent lock means token holders—not LPs—bear this risk, while also capturing trading fee benefits.

## What is Impermanent Loss

When providing liquidity to Uniswap, the pool automatically rebalances holdings as prices change. If token price increases relative to USDC, the pool sells tokens and buys USDC. If price decreases, it buys tokens and sells USDC.

This rebalancing causes LP value to underperform simply holding the original assets when prices move significantly.

## The Mathematics

Uniswap maintains constant product:

$$
K = x \times y
$$

Where $x$ = USDC amount, $y$ = token amount.

At any price $P = x/y$, the pool holds:

$$
y = \sqrt{\frac{K}{P}}
$$

$$
x = \sqrt{K \times P}
$$

### Initial State

LP provides \$25,000 USDC and 625,000 tokens at $P_0 = 0.04$:

$$
K = 25,000 \times 625,000 = 15,625,000,000
$$

### After Price Increase

If price rises to $P_1 = 0.16$ (4x increase):

$$
y_1 = \sqrt{\frac{15,625,000,000}{0.16}} = 312,500 \text{ tokens}
$$

$$
x_1 = \sqrt{15,625,000,000 \times 0.16} = 50,000 \text{ USDC}
$$

**LP value**: \$50,000 + (312,500 × \$0.16) = \$100,000

**Hold value**: \$25,000 + (625,000 × \$0.16) = \$125,000

**Impermanent loss**: \$125,000 - \$100,000 = \$25,000 (20%)

## IL Formula

General impermanent loss for price multiplier $k$:

$$
\text{IL} = \frac{2\sqrt{k}}{1 + k} - 1
$$

| Price Change | IL Percentage |
|--------------|---------------|
| 1.25x | -0.6% |
| 1.5x | -2.0% |
| 2x | -5.7% |
| 3x | -13.4% |
| 4x | -20.0% |
| 5x | -25.5% |
| 10x | -42.0% |

Larger price movements create larger IL.

## Who Bears IL Risk

In DeLong, LP tokens are permanently locked in governance, not held by individual LPs:

**Locked LP position**:
- Governance holds LP tokens
- LP value subject to impermanent loss
- Loss distributed across all token holders proportionally
- No individual "LP" to suffer concentrated IL

**Token holders collectively**:
- Own proportional share of locked LP
- Bear IL risk proportional to holdings
- Also capture trading fee benefits
- Can exit via secondary market (Uniswap), not LP redemption

This differs from traditional LPs who concentrate IL risk while earning fees.

## IL vs Trading Fees

While IL reduces LP value relative to holding, trading fees partially offset losses:

**Fee accumulation**:
$$
\text{Fees} = \text{Trading Volume} \times 0.003
$$

**Net outcome**:
$$
\text{LP Value} = \text{Base Value} - \text{IL} + \text{Accumulated Fees}
$$

High trading volume can offset or exceed IL, making LP positions profitable despite price divergence.

### Example with Fees

Continuing previous example (4x price increase, 20% IL):

**Without fees**: \$100,000 LP value (20% IL)

**With \$5M trading volume**:
- Fees: \$5M × 0.003 = \$15,000
- LP value: \$100,000 + \$15,000 = \$115,000
- Net IL: \$125,000 - \$115,000 = \$10,000 (8%)

**With \$10M trading volume**:
- Fees: \$10M × 0.003 = \$30,000
- LP value: \$100,000 + \$30,000 = \$130,000
- **Profit**: \$130,000 - \$125,000 = +\$5,000 despite 4x price change

Sufficient trading volume converts impermanent loss into net gain.

## Strategic Implications

Token holders should understand IL affects locked LP value:

**Price increases**: LP sells tokens into strength, reducing token exposure but increasing USDC—good for delisting refunds, bad for upside capture

**Price decreases**: LP buys tokens in weakness, increasing token exposure but reducing USDC—bad for delisting refunds, increases future upside

**High volatility**: Larger IL but potentially more trading volume and fees

**Stable prices**: Minimal IL, LP value approximates initial allocation

## IL in Delisting

When projects delist, recovered USDC from LP removal reflects IL effects:

**Scenario 1**: Price unchanged from launch
- LP holds original USDC amount + trading fees
- High recovery rate

**Scenario 2**: 5x price increase
- LP sold 77% of tokens, holds ~3x USDC + fees
- Very high recovery rate (USDC-heavy)

**Scenario 3**: 80% price decline
- LP bought tokens, holds ~50% USDC + fees
- Lower recovery rate (token-heavy, low value)

IL affects delisting economics significantly—upward price movement before failure improves refund rates, downward movement worsens them.

## Comparison to Traditional LP

**Traditional Uniswap LP**:
- Individual decision to provide liquidity
- Bears full IL risk on their capital
- Earns all trading fees on their position
- Can remove liquidity anytime

**DeLong Locked LP**:
- Automatic LP creation at launch
- IL distributed across all token holders
- Trading fees enhance delisting refunds
- Cannot remove until delisting vote

The permanant lock converts concentrated LP risk/reward into distributed token holder exposure.
