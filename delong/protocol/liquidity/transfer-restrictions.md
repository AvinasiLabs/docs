# Transfer Restrictions

_Author: Dylan, Avinasi Labs_

Token transfers remain frozen during fundraising to prevent premature circulation before trading infrastructure exists. This protects investors from market fragmentation and ensures orderly launch.

## Why Freeze During Fundraising

IDO participants receive tokens immediately upon purchase, but cannot transfer them until launch completes. This prevents:

**Market fragmentation** - Without trading infrastructure, early token holders could attempt peer-to-peer sales at arbitrary prices disconnected from the ongoing fundraising mechanism. This fragments price discovery and disadvantages later participants.

**Pre-launch speculation** - Enabling transfers before Uniswap pool creation would create speculative secondary markets with no liquidity backing. Investors would face uncertain exit conditions and volatile pricing.

**Launch coordination failures** - Tokens circulating before treasury deposits and LP creation complete could create states where tokens exist but project operations cannot begin. Freezing ensures atomic transition from fundraising to full operation.

## How It Works

The token contract enforces transfer restrictions at the contract level. All transfer operations revert during fundraising except for minting tokens to IDO participants during purchases.

When the Raise Target is reached and launch executes, the protocol atomically enables transfers alongside Uniswap pool creation. This ensures trading infrastructure exists before tokens become transferable.

## What This Means for Investors

During fundraising, tokens purchased through the IDO appear in your wallet balance but cannot be sent to other addresses or traded. You must wait for launch completion before:

- Trading tokens on Uniswap
- Transferring to other wallets
- Using tokens in any DeFi protocols

This freeze is not a limitation but a protection—it ensures you can trade at fair prices backed by real liquidity rather than speculating in fragmented pre-launch markets. All investors exit the IDO on equal footing when transfers enable simultaneously with liquidity deployment.

After launch completes, tokens become fully transferable ERC-20 assets with no restrictions.
