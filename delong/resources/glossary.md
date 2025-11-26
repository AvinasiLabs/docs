# Glossary

_Author: Dylan, Avinasi Labs_

Key terms used in DeLong Protocol documentation.

## IDO (Initial Data Offering)

Fundraising mechanism using Virtual AMM for continuous price discovery during 100-day period.

## Raise Target

The target amount of capital (in USDC) that a project aims to raise through its IDO. When this target is reached, the protocol automatically launches the project by creating a Uniswap liquidity pool and depositing treasury funds under governance control.

## Initial Equity

The actual number of tokens allocated to project teams based on the Initial Equity Ratio. Calculated as $$S_{LP} = \alpha \times S_{total}$$, where $$S_{total}$$ is the total token supply.

## Initial Equity Ratio

The fraction of total token supply ($$\alpha$$) that project teams receive in exchange for contributing the dataset as the underlying asset. These tokens are permanently locked in the Uniswap liquidity pool but retain revenue rights (rental dividends) and governance rights (voting power). This parameter reflects the relative valuation of the dataset asset versus external capital needs.

## Delisting

The formal process for shutting down failed or abandoned projects through democratic voting. Requires a delisting proposal to pass with 50%+ approval. Upon execution, the protocol removes the locked Uniswap LP, combines recovered USDC with remaining treasury funds, and enables proportional refund claims for all token holders. Typically recovers 40-90% of initial capital depending on treasury usage.

## TEE (Trusted Execution Environment)

Confidential computing environment for secure dataset access control.
