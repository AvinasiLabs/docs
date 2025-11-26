# Price Discovery

Token price rises continuously as investors participate in the IDO. Earlier participants receive more tokens per dollar invested than later ones, creating automatic rewards for early commitment without manual tiering or arbitrary price schedules.

## Trading Fees

The protocol charges fees on all IDO transactions to support protocol operations and prevent spam:

**Purchase fee**: 0.3% on all token purchases
- Applied to USDC amount before calculating tokens received
- Collected by protocol treasury, not the project
- Example: 1,000 USDC purchase pays 3 USDC fee, 997 USDC buys tokens

**Sale fee**: 0.5% on all token sales back to the virtual AMM
- Applied to USDC refund amount after calculating from constant product
- Collected by protocol treasury
- Example: Selling tokens for 1,000 USDC receives 995 USDC after 5 USDC fee

These fees are substantially lower than typical DEX trading fees (0.3% Uniswap) while discouraging short-term speculation during fundraising.

## How Pricing Works

The protocol uses Uniswap-style constant product pricing integrated with virtual reserves (see [virtual-amm.md](virtual-amm.md) for technical details). This section focuses on pricing from the investor perspective and its fairness properties.

## Marginal Price

The price an investor pays depends on where they participate in the fundraising sequence. The marginal price at any point equals the ratio of reserves:

$$
P = \frac{x}{y}
$$

As USDC accumulates ($$x$$ increases) and tokens are distributed ($$y$$ decreases), this ratio grows continuously.

### Price Evolution Example

Consider a dataset starting with virtual reserves $$x_0 = 2,525$$ USDC and $$y_0 = 252,525$$ tokens:

| Investment | $$x$$ (USDC) | $$y$$ (tokens) | Price (USDC/token) |
|-----------|-----------|-------------|-------------------|
| Start | 2,525 | 252,525 | 0.0100 |
| After 5,000 USDC | 7,525 | 84,842 | 0.0887 |
| After 10,000 USDC | 12,525 | 50,901 | 0.2461 |
| After 25,000 USDC | 27,525 | 23,157 | 1.1887 |
| After 50,000 USDC | 52,525 | 12,142 | 4.3255 |

The price increases from \$0.01 to over \$4.00—a 400x+ appreciation—demonstrating the strong advantage of early participation.

## Average Purchase Price

While the marginal price represents the instantaneous exchange rate, investors actually pay an average price across their entire purchase due to price impact. For a purchase of $$\Delta x$$ USDC receiving $$\Delta y$$ tokens, the average price paid is:

$$
P_{avg} = \frac{\Delta x}{\Delta y}
$$

This average price always exceeds the marginal price before the trade but is less than the marginal price after the trade.

### Example: Purchase Impact

Starting state: $$x = 10,000$$ USDC, $$y = 100,000$$ tokens, $$K = 1,000,000,000$$

**Small purchase (100 USDC):**
- Tokens received: $$\Delta y = \frac{100,000 \times 100}{10,000 + 100} = 990.1$$ tokens
- Average price: $$\frac{100}{990.1} = 0.101$$ USDC/token
- Price before: $$\frac{10,000}{100,000} = 0.100$$
- Price after: $$\frac{10,100}{99,010} = 0.102$$

**Large purchase (1,000 USDC):**
- Tokens received: $$\Delta y = \frac{100,000 \times 1,000}{10,000 + 1,000} = 9,090.9$$ tokens
- Average price: $$\frac{1,000}{9,090.9} = 0.110$$ USDC/token
- Price before: \$0.100$
- Price after: $$\frac{11,000}{90,909} = 0.121$$

The large purchase pays 10% more than the marginal price before purchase, while the small purchase pays only 1% more. This price impact discourages single large purchases and naturally distributes token ownership.

## Early Participation Advantage

The advantage of early participation is deterministic and verifiable. Investors who participate earlier receive fundamentally better terms:

**First 10% of raise** – Purchase near initial \$0.01 pricing when token supply is abundant. Receive maximum tokens per USDC.

**Middle 50% of raise** – Pay moderately higher prices as reserves shift. Still receive favorable terms compared to late participants.

**Final 40% of raise** – Purchase close to launch pricing when most tokens have been distributed. Receive fewer tokens per USDC but help complete the fundraising goal.

## Protection Against Manipulation

The continuous pricing model prevents several forms of manipulation common in traditional fundraising:

### No Front-Running

All purchases execute at prices determined by current reserve states calculated from on-chain data. No ability to observe pending transactions and insert orders ahead of them, because price calculation is deterministic based on the blockchain state at execution time.

### No Whale Dumps

Large purchases experience price impact proportional to size. A single large purchase cannot extract value at constant low prices—it must pay increasingly higher average prices as it consumes available token supply.

The constant product formula ensures that purchasing 10,000 USDC worth of tokens in a single transaction costs significantly more than purchasing 1,000 USDC ten separate times.

### No Insider Pricing

Teams cannot grant preferential prices to specific investors. All participants face the same pricing curve determined by the mathematical formula and their position in the purchase sequence. The pricing mechanism treats all addresses identically.

### Transparent Execution

All price calculations follow deterministic formulas verifiable on-chain. Investors can confirm they received correct token amounts by:

1. Querying reserve states before and after their purchase
2. Calculating expected token amount using constant product formula
3. Comparing calculated amount to actual received amount
4. Verifying the transaction executed correctly

## Pricing vs Fixed Sales

Traditional fixed-price token sales require teams to predict fair pricing before knowing market demand. This creates systematic misalignment:

**Overpriced sales** fail to raise target capital, leaving projects underfunded and often cancelled. **Underpriced sales** complete quickly but leave significant value on the table, primarily benefiting bots and front-runners rather than actual supporters.

The continuous discovery model eliminates this prediction requirement. The final price emerges organically from actual demand patterns observed during fundraising. Teams don't need to guess valuations—the market reveals them through participation.

### Fairness Properties

The pricing mechanism achieves several fairness objectives:

**Time-based advantage** – Supporters who commit capital early receive proportionally better terms. This rewards conviction and research rather than capital size alone.

**Deterministic outcomes** – All pricing follows mathematical rules without discretionary decisions. No team favoritism, insider deals, or preferential access possible.

**Size-proportional impact** – Larger purchases pay higher average prices due to slippage through the bonding curve, preventing single actors from extracting disproportionate value.

**Verifiable execution** – All calculations are transparent and checkable on-chain. Investors can prove they received correct allocations by reproducing the mathematics.

These properties create a level playing field where early research and commitment provide advantages rather than insider access or bot sophistication.
