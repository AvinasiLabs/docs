# Protocol Overview

DeLong Protocol provides economic infrastructure for tokenizing revenue-generating datasets. The protocol handles fundraising, treasury custody, liquidity guarantees, rental payments, and dividend distribution through five integrated modules.

## IDO Mechanism

Projects raise capital through Initial Data Offerings with continuous price discovery. Earlier participants receive better pricing as token price rises with demand. When the funding goal is reached, raised funds are automatically split between creating a permanent Uniswap liquidity pool and depositing treasury reserves under governance control.

## Governance

Token holders control treasury allocations, operational parameters, and project continuation through on-chain voting (see [governance/overview.md](governance/overview.md) for complete governance model). Proposals require majority support to execute. No privileged roles exist—all fund withdrawals require governance approval regardless of circumstances.

## Liquidity

Uniswap LP tokens created at launch are permanently locked in the governance contract. This guarantees secondary market trading throughout the project lifecycle. Teams cannot remove liquidity under any circumstances. The only removal path requires formal delisting through majority vote.

## Rental & Dividends

Researchers pay rental fees to access datasets. Revenue is automatically split between token holders and protocol operations. Dividends distribute proportionally to all token holders without manual processing or batch settlements.

## Smart Contracts

Each dataset receives an isolated contract suite deployed through gas-efficient minimal proxies. The suite includes token contract, IDO contract, governance contract, and rental pool contract. A factory coordinates deployment and maintains the protocol-wide dataset registry.
