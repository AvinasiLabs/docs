# DatasetToken Contract

_Author: Dylan, Avinasi Labs_

The DatasetToken implements ERC-20 with voting (ERC20Votes), freezing mechanism, and automatic dividend settlement through RentalPool integration.

## State Variables

**Token metadata:**
- `_tokenName` - Custom name set in initialize()
- `_tokenSymbol` - Custom symbol set in initialize()

**Associated contracts:**
- `rentalPool` - RentalPool address for dividend distribution
- `idoContract` - IDO address that can freeze/unfreeze

**Freezing state:**
- `isFrozen` - Whether transfers are currently restricted
- `frozenExempt[address]` - Addresses exempt from freezing (IDO, Factory, Uniswap pool)

## Initialization

The Factory calls `initialize` after cloning with:
- Token name and symbol
- Initial owner (Factory)
- IDO contract address
- RentalPool address
- Initial supply (minted directly to IDO contract)

The token starts in frozen state. Factory and IDO are marked as exempt from freezing.

## Freezing Mechanism

During IDO (Active status), token transfers revert unless sender or receiver is exempt.

Exemptions:
- IDO contract (for minting/burning)
- Factory (initial setup)
- Uniswap pool (added at launch via `addFrozenExempt`)

The IDO calls `unfreeze()` during launch execution. This operation is irreversible—tokens remain unfrozen permanently.

## Dividend Settlement

The `_update` hook intercepts all transfers (including mints/burns) to settle dividends:

**Before balance change:**
1. Call `RentalPool.beforeBalanceChange(from, balanceOf(from))` if from ≠ 0
2. Call `RentalPool.beforeBalanceChange(to, balanceOf(to))` if to ≠ 0

This settles pending dividends based on old balances and adds them to `pendingClaim`.

**Execute transfer:**
Parent `ERC20Votes._update` updates balances and voting checkpoints.

**After balance change:**
1. Call `RentalPool.afterBalanceChange(from, balanceOf(from))` if from ≠ 0
2. Call `RentalPool.afterBalanceChange(to, balanceOf(to))` if to ≠ 0

This updates `rewardDebt` baseline based on new balances.

This two-phase settlement ensures fair dividend distribution during transfers.

## Voting Power

ERC20Votes maintains historical balance checkpoints for governance. When creating proposals or voting, the Governance contract queries voting power at a past block:

$$
\text{Voting Power} = \text{getPastVotes}(user, block.number - 1)
$$

The Governance contract uses this for proposal creation threshold (1% of supply) and quorum calculations (50% of supply).

## Owner Functions

The Factory (owner) can:
- `addFrozenExempt(address)` - Add address to exemption list (used for Uniswap pool at launch)
- `removeFrozenExempt(address)` - Remove address from exemption list

The IDO contract (not owner) can:
- `unfreeze()` - Permanently unfreeze token transfers (called at launch)

No other privileged functions exist. The owner cannot:
- Mint additional tokens
- Burn tokens from addresses
- Pause or block transfers after unfreezing
- Modify voting power checkpoints

## Integration Points

**IDO contract** - Receives entire initial supply, handles minting/burning during fundraising.

**RentalPool** - Receives balance change notifications for dividend accounting.

**Governance** - Queries voting power checkpoints for proposal thresholds and voting.

**Uniswap** - Added to frozen exemptions at launch, enabling secondary market trading.

## Events

**Unfrozen(timestamp)** - Emitted when tokens are permanently unfrozen at launch.

**RentalPoolSet(rentalPool)** - Emitted when RentalPool address is set during initialization.

Standard ERC20 Transfer/Approval events also emitted for wallet compatibility.

## Decimal Precision

The token uses 18 decimals (standard ERC20), while USDC uses 6 decimals. The IDO contract handles decimal conversion when calculating prices and costs.
