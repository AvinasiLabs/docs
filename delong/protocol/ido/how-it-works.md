# How IDO Works

The Initial Data Offering enables projects to raise capital through a fair, continuous pricing mechanism that rewards early participation.

## Basic Flow

**1. Project Setup**

Project teams configure two parameters:
- **Raise Target** - How much capital to raise (e.g., $50,000 USDC)
- **Initial Equity Ratio** - Dataset asset's equity share (e.g., 20%)

The protocol calculates total token supply and initializes a Virtual AMM starting at $0.01 per token.

**2. Fundraising Period**

Investors purchase tokens through the Virtual AMM:
- Token price rises continuously as USDC accumulates
- Earlier investors receive more tokens per dollar
- All transactions are permissionless and on-chain
- 100-day maximum fundraising window

**3. Automatic Launch**

When the Raise Target is reached, the protocol automatically:
- Creates a Uniswap V2 liquidity pool (USDC + tokens)
- Locks LP tokens permanently in Governance contract
- Deposits remaining USDC into governance-controlled treasury
- Unfreezes token transfers, enabling secondary trading

**4. Post-Launch Operation**

- Researchers pay rental fees to access the dataset
- 95% of rental revenue flows to token holders as dividends
- Token holders vote on treasury spending through governance proposals
- Project teams operate the dataset and receive rental dividends from their Initial Equity

## What Happens If Fundraising Fails

If the Raise Target isn't reached within 100 days, investors can claim proportional refunds. The protocol returns accumulated USDC based on each investor's token holdings.
