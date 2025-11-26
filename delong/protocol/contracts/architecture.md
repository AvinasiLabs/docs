# Smart Contract Architecture

> Source code: [AvinasiLabs/delong-v1](https://github.com/AvinasiLabs/delong-v1)

DeLong implements five core contracts per dataset: Factory, IDO, DatasetToken, Governance, and RentalPool. The Factory deploys minimal proxies for gas efficiency, while each dataset operates independently with its own contract suite.

## Contract Suite

**Factory** - Deploys new datasets using EIP-1167 minimal proxy pattern. Maintains implementation addresses and emits events for indexing. Configured once with protocol fee recipient and Uniswap addresses.

**IDO** - Implements Virtual AMM fundraising with constant product pricing from \$0.01 initial price. Handles token trading during 100-day period, triggers automatic launch when all salable tokens sell, manages rental payments and access verification, and coordinates LP creation plus fund distribution.

**DatasetToken** - ERC-20 token with ERC20Votes governance extensions. Freezes transfers during fundraising, unfreezes at launch. Integrates with RentalPool via `_update` hook for automatic dividend settlement on all transfers.

**Governance** - Merges treasury custody, LP management, and voting into single contract. Implements four proposal types (Funding, Pricing, Delist, GovernanceUpgrade) with 7-day voting periods and 50% quorum. Holds USDC treasury and locked Uniswap LP tokens, only releases via approved proposals.

**RentalPool** - Distributes dividends using Accumulated Rewards algorithm. Receives 95% of rental payments from IDO, maintains O(1) complexity for revenue addition and claims, settles dividends through DatasetToken hooks during transfers.

## Contract Dependencies

**Factory** → deploys → IDO, DatasetToken, Governance, RentalPool

**IDO** → calls:
- `DatasetToken.unfreeze()` at launch
- `Governance.depositFunds()` and `lockLP()` at launch
- `RentalPool.addRevenue()` on rental payments

**DatasetToken** → calls:
- `RentalPool.beforeBalanceChange()` and `afterBalanceChange()` on transfers

**Governance** → calls:
- `IDO.updateHourlyRate()` on Pricing proposals
- Uniswap router for LP removal on Delist proposals

## Decimal Handling

**USDC**: 6 decimals (1 USDC = 1e6 smallest units)

**Dataset tokens**: 18 decimals (1 token = 1e18 smallest units)

**Prices**: USDC per token (6 decimals)

**Dividend precision**: 1e18 scaling factor for accurate per-token accounting

The IDO and RentalPool handle decimal conversions internally. Frontend applications must use proper decimal scaling when calling contract functions.

## Event Indexing

Off-chain systems monitor events to build application state:

**Dataset catalog:** `IDOCreated` events from Factory

**Fundraising progress:** `TokensPurchased`, `TokensSold`, `IDOLaunched` from IDO

**Governance activity:** `ProposalCreated`, `VoteCast`, `ProposalExecuted` from Governance

**Revenue tracking:** `AccessPurchased`, `RentalDistributed`, `RevenueAdded` from IDO and RentalPool

**Dividend claims:** `DividendsClaimed` from RentalPool

This event-driven architecture enables responsive frontends without constant state polling.
