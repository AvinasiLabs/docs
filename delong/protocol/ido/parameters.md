# Parameters & Constraints

Project teams configure two fundamental parameters when launching an IDO: the funding goal and team allocation percentage. The protocol enforces constraints on these parameters to ensure viable token economies and investor protection.

## Funding Goal

The funding goal ($R_{target}$) determines how much capital the project aims to raise. This target defines when the IDO completes and how raised funds split between liquidity pool creation and treasury deposit.

**Protocol constraints:**
- Must be greater than zero (any positive USDC amount is valid)
- No maximum limit enforced by the protocol

Teams have complete flexibility in setting funding goals based on actual project needs. Data collection costs, infrastructure expenses for hosting and access, ongoing maintenance requirements, and development buffers all factor into appropriate funding levels. The governance-controlled treasury means teams must justify all expenditures to token holders, encouraging realistic rather than inflated funding requests.

Typical funding goals range from \$10,000 for small datasets to \$500,000+ for large-scale data collection efforts, but the protocol imposes no hard boundaries.

## Team Allocation

The team allocation percentage ($\alpha$) determines what fraction of total token supply the project team receives. This allocation rewards teams for dataset creation and ongoing maintenance while aligning their interests with token holder success.

**Protocol constraints:**
- Minimum: 0.01% (1 basis point) to allow maximum flexibility
- Maximum: 50% (5000 basis points) to prevent majority control

The mathematically valid range extends up to 50%, though typical projects use 15-30% allocations. Lower allocations (15-20%) suit early-stage datasets requiring significant community-funded development. Higher allocations (25-30%) compensate teams that have already completed substantial work before fundraising. Extreme values (0.01-15% or 30-50%) are permitted but uncommon.

## Initial Price

All IDOs start at a standardized initial price of $P_0 = 0.01$ USDC per token. This uniform starting point provides several investor benefits:

**Comparability** – Investors can directly compare different dataset offerings without adjusting for arbitrary starting valuations. All projects begin from the same baseline.

**Fair discovery** – Final pricing emerges purely from market demand rather than team predictions or manipulations. No anchoring effects from initial price choices.

**Accessibility** – Low entry price enables broad participation regardless of project scale or investor capital.

## Supply Calculation

The protocol calculates total token supply using a geometric mean formula that balances multiple requirements:

$$
S_{total} = \frac{R_{target}}{P_0} \cdot \sqrt{\frac{\alpha}{(1-\alpha)^3}}
$$

This formula ensures that:
1. The public sale can raise exactly $R_{target}$ through the virtual AMM
2. The LP receives adequate token allocation for meaningful liquidity depth
3. The capital split between LP and treasury follows optimal ratios

### Supply Components

Total supply splits into three allocations:

$$
S_{sale} = (1 - \alpha) \cdot S_{total}
$$

$$
S_{LP} = \alpha \cdot S_{total}
$$

Where:
- $S_{sale}$ = Tokens sold to public investors during the IDO
- $S_{LP}$ = Tokens reserved for Uniswap liquidity pool pairing
- $S_{total}$ = $S_{sale}$ + $S_{LP}$

### Example Calculation

For a dataset with $R_{target} = 50,000$ USDC and $\alpha = 0.20$:

$$
S_{total} = \frac{50,000}{0.01} \cdot \sqrt{\frac{0.20}{(0.80)^3}} = 5,000,000 \cdot \sqrt{\frac{0.20}{0.512}}
$$

$$
= 5,000,000 \cdot \sqrt{0.3906} = 5,000,000 \cdot 0.625 = 3,125,000 \text{ tokens}
$$

Supply allocation:
- $S_{sale} = 0.80 \times 3,125,000 = 2,500,000$ tokens
- $S_{LP} = 0.20 \times 3,125,000 = 625,000$ tokens

## Capital Distribution at Launch

When fundraising completes, raised capital splits between creating the Uniswap LP and depositing treasury funds. The split ratio depends on the team allocation:

$$
\gamma_{LP} = \sqrt{\frac{\alpha}{1-\alpha}}
$$

$$
USDC_{LP} = R_{target} \cdot \gamma_{LP}
$$

$$
USDC_{treasury} = R_{target} \cdot (1 - \gamma_{LP})
$$

### Example Distribution

Continuing the previous example with $\alpha = 0.20$:

$$
\gamma_{LP} = \sqrt{\frac{0.20}{0.80}} = \sqrt{0.25} = 0.50
$$

Capital split:
- $USDC_{LP} = 50,000 \times 0.50 = 25,000$ USDC to Uniswap LP
- $USDC_{treasury} = 50,000 \times 0.50 = 25,000$ USDC to governance treasury

The LP pairs 25,000 USDC with 625,000 tokens, establishing a launch price of:

$$
P_{launch} = \frac{25,000}{625,000} = 0.04 \text{ USDC per token}
$$

This represents a 4x appreciation from the initial \$0.01 price, rewarding early IDO participants.

## Alpha Impact on Outcomes

The team allocation percentage significantly affects fundraising dynamics and investor outcomes. The following table shows how different $\alpha$ values impact capital distribution for a \$50,000 USDC raise:

| $\alpha$ | $\gamma_{LP}$ | LP USDC | Treasury USDC | LP Tokens | Launch Price |
|----------|---------------|---------|---------------|-----------|--------------|
| 15% | 0.420 | 21,000 | 29,000 | 468,750 | \$0.0448 |
| 20% | 0.500 | 25,000 | 25,000 | 625,000 | \$0.0400 |
| 25% | 0.577 | 28,867 | 21,133 | 781,250 | \$0.0370 |
| 30% | 0.655 | 32,732 | 17,268 | 937,500 | \$0.0349 |

### Key Observations

**Lower alpha (15-20%)**
- Larger public sale allocation means more tokens sold during IDO
- More treasury funds available for operations (58% of raise)
- Less capital consumed by LP creation
- Higher launch price (4.48x vs 3.49x appreciation)

**Higher alpha (25-30%)**
- Smaller public sale allocation means fewer tokens sold
- Less treasury funds (only 35% of raise at α=30%)
- Deeper initial liquidity pools improve trading conditions
- Lower launch price reduces IDO appreciation

## Price Appreciation During IDO

The virtual AMM pricing causes continuous price appreciation from $P_0 = 0.01$ to $P_{launch}$. Early investors purchasing at the beginning receive tokens near the initial price, while later investors pay increasingly higher prices. The final launch price depends on the team allocation:

| $\alpha$ | Launch Price | Price Multiplier |
|----------|--------------|------------------|
| 15% | \$0.0448 | 4.48x |
| 20% | \$0.0400 | 4.00x |
| 25% | \$0.0370 | 3.70x |
| 30% | \$0.0349 | 3.49x |

Early investors receive more favorable pricing when team allocations are lower, since more tokens are distributed through the public sale phase, creating stronger price appreciation from initial to final pricing.

## Parameter Selection Guidance

Projects should select parameters based on their specific circumstances:

**Completed datasets with proven utility** – Higher alpha (25-30%) justified by existing value. Can operate with smaller treasuries since dataset already exists and requires mainly maintenance.

**Early-stage datasets requiring development** – Lower alpha (15-20%) ensures adequate treasury for community-funded development. Need operational runway to complete dataset construction.

**High infrastructure costs** – Lower alpha leaves more funds available for expensive hosting, compute, and data acquisition expenses.

**Mature projects with stable operations** – Higher alpha captures completed work value with less treasury dependence. Minimal ongoing costs allow smaller operational budgets.

The protocol's constraint ranges ensure all parameter combinations produce functional token economies while protecting investor interests through minimum liquidity requirements and maximum team allocation limits.
