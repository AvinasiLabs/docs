# Virtual AMM Construction

_Author: Dylan, Avinasi Labs_

The protocol must enable price discovery starting from an initial price when no actual liquidity exists yet. The virtual AMM solves this cold start problem by initializing trading with mathematically constructed reserves that create the desired starting price.

## The Cold Start Problem

Traditional automated market makers require existing liquidity to function. Someone must deposit paired assets before trading can begin. This creates a chicken-and-egg problem for new token launches: no one can trade until liquidity exists, but creating initial liquidity requires determining fair pricing before any market discovery has occurred.

Fixed-price sales avoid this by setting prices administratively, but sacrifice market-determined valuation. The virtual AMM enables price discovery from the start while maintaining constant product mechanics throughout fundraising.

## Virtual Reserve Concept

The protocol initializes fundraising with virtual reserves—mathematically calculated values that establish the initial price without requiring actual asset deposits. The constant product formula operates on the sum of virtual and real reserves:

$$
K = (x_{virtual} + x_{real}) \times (y_{virtual} + y_{real})
$$

At the beginning of fundraising:
- $$x_{real} = 0$$ (no USDC collected yet)
- $$y_{real} = 0$$ (no tokens distributed yet)
- $$(x_{virtual}, y_{virtual})$$ are calculated to produce the desired starting price

As investors purchase tokens, real USDC accumulates and real tokens are distributed. The pricing mechanism treats $$(x_{virtual} + x_{real})$$ and $$(y_{virtual} + y_{real})$$ as the effective reserves, enabling smooth price appreciation from the initial state through full capitalization.

## Starting Price Construction

All IDOs begin at $$P_0 = 0.01$$ USDC per token. The virtual reserves must satisfy:

$$
\frac{x_{virtual}}{y_{virtual}} = P_0 = 0.01
$$

This constraint alone is insufficient—we need a second requirement. The virtual reserves are constructed such that when exactly $$R_{target}$$ USDC is collected, exactly $$S_{sale}$$ tokens have been sold according to the constant product formula.

## Mathematical Constraints

The virtual reserve construction must satisfy three requirements simultaneously:

1. **Initial price**: $$\frac{x_0}{y_0} = 0.01$$

2. **Fundraising completion**: When $$R_{target}$$ USDC is added, $$S_{sale}$$ tokens are sold

3. **Constant product preservation**: $$K = x_0 \times y_0 = (x_0 + R_{target}) \times (y_0 - S_{sale})$$

From constraint (3):
$$
x_0 \cdot y_0 = (x_0 + R_{target}) \cdot (y_0 - S_{sale})
$$

Expanding:
$$
x_0 \cdot y_0 = x_0 \cdot y_0 - x_0 \cdot S_{sale} + R_{target} \cdot y_0 - R_{target} \cdot S_{sale}
$$

Simplifying:
$$
0 = -x_0 \cdot S_{sale} + R_{target} \cdot y_0 - R_{target} \cdot S_{sale}
$$

$$
x_0 \cdot S_{sale} = R_{target}(y_0 - S_{sale})
$$

Combined with constraint (1), $$x_0 = 0.01 \cdot y_0$$:

$$
0.01 \cdot y_0 \cdot S_{sale} = R_{target}(y_0 - S_{sale})
$$

This equation, along with the supply formula $$S_{total} = S_{sale} + S_{LP}$$ and the geometric mean calculation, determines the unique values of $$(x_0, y_0)$$ that satisfy all constraints.

## Reserve Transition

Throughout fundraising, the effective reserves shift from entirely virtual toward entirely real. The transition demonstrates how the mechanism bridges cold start to full liquidity:

| Fundraising Progress | Virtual Weight | Real Weight | Mechanism Behavior |
|---------------------|----------------|-------------|-------------------|
| 0% complete | 100% virtual | 0% real | Pure virtual AMM, enables trading from zero |
| 25% complete | ~75% virtual | ~25% real | Mostly virtual, small real accumulation |
| 50% complete | ~50% virtual | ~50% real | Balanced mix of virtual and real |
| 75% complete | ~25% virtual | ~75% real | Mostly real, approaching launch |
| 100% complete | Deprecated | 100% real | Virtual reserves no longer needed |

When fundraising completes and the protocol creates the actual Uniswap pool, it uses only real tokens and real USDC. The virtual reserves have served their purpose of enabling price discovery and are no longer relevant to post-launch trading.

## Why This Construction Works

The geometric mean formula for total supply ($$S_{total}$$) and the virtual reserve construction are mathematically linked to ensure consistency. The formula:

$$
S_{total} = \frac{R_{target}}{P_0} \cdot \sqrt{\frac{\alpha}{(1-\alpha)^3}}
$$

directly determines both:
- How many tokens must be created ($$S_{total}$$)
- What virtual reserves enable selling exactly $$(1-\alpha) \cdot S_{total}$$ tokens for $$R_{target}$$ USDC

This linkage guarantees that:
1. Fundraising completes when target capital is raised
2. The LP receives exactly $$\alpha \cdot S_{total}$$ tokens for pairing
3. Capital splits correctly between LP and treasury
4. Launch price matches the final virtual AMM price

## Benefits for Investors

The virtual AMM construction provides several protections and advantages:

**Immediate price discovery** – Trading begins without requiring anyone to provide initial liquidity or guess at fair starting valuations. The market discovers pricing through actual participation.

**Smooth appreciation** – Price increases continuously from the starting point rather than jumping between arbitrary tiers or fixed price levels. No sudden changes that advantage timing luck over conviction.

**Deterministic pricing** – All investors face prices calculated from the same mathematical foundation without preferential access or insider rates. The formulas treat all addresses identically.

**Comparable valuations** – The standardized $$P_0 = 0.01$$ starting price enables direct comparison between different dataset offerings without adjusting for arbitrary team-chosen initial valuations.

**No liquidity provider risk** – Unlike traditional AMM launches where early LPs face impermanent loss, the virtual AMM requires no initial LP deposits. Teams don't risk capital to bootstrap liquidity.

**Guaranteed completion** – The mathematical linkage between virtual reserves, supply, and fundraising target ensures that reaching $$R_{target}$$ automatically triggers launch. No uncertainty about whether goals will be met given sufficient demand.

## Cold Start Solution

The virtual AMM converts the cold start problem from a limitation into an advantage. Instead of requiring someone to:
- Guess fair initial pricing
- Risk capital in initial LP deposits
- Manually adjust pricing based on demand
- Coordinate launch timing

The protocol:
- Determines pricing mathematically from demand
- Requires zero initial capital deposits
- Adjusts pricing automatically through constant product
- Launches atomically upon fundraising completion

This automation removes trust requirements and coordination problems while enabling fair price discovery from zero liquidity.
