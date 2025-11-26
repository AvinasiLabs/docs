# Liquidity Management

DeLong implements permanent liquidity locking to eliminate rug pull risks while enabling capital recovery for failed projects through accumulated trading fees.

When an IDO launches, the protocol creates a Uniswap V2 liquidity pool pairing dataset tokens with USDC. The LP tokens representing this position are minted directly to the Governance contract rather than any team-controlled address. The Governance contract provides no function for transferring LP tokens except the internal delisting execution logic that removes liquidity as part of investor refund distribution. This makes LP locking a mathematical property of the contract code—teams cannot remove liquidity regardless of intentions or circumstances.

Uniswap V2 charges 0.3% fees on all trades, accumulating in pool reserves rather than extracting them. These fees increase LP value over time, creating a cushion that enhances delisting refunds even for failed projects. Price divergence between paired assets creates impermanent loss for LPs, which only realizes if LP is removed via delisting. Anyone can verify LP custody on-chain by querying balances and reading contract code—no trust required.
