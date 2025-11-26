# Rental Payments

_Author: Dylan, Avinasi Labs_

Researchers pay hourly fees to access datasets in TEE environments. Payments split 95% to token holder dividends and 5% to protocol fees.

## How Rental Works

Researchers purchase dataset access by calling the rental payment function on the IDO contract:

1. Specify hours of access to purchase
2. Pay USDC amount = hours × hourly rate
3. Contract extends access expiration timestamp
4. Payment splits: 95% to dividend pool, 5% to protocol

Access is cumulative—purchasing additional hours extends existing access rather than replacing it. No minimum or maximum limits exist, though gas costs make very small purchases (1-2 hours) inefficient.

## Revenue Distribution

Each payment splits automatically:

- **95% to token holders**: $$\text{Dividend Amount} = \text{Payment} \times 0.95$$
- **5% to protocol**: $$\text{Protocol Fee} = \text{Payment} \times 0.05$$

The 95% transfers to the RentalPool contract, which updates the dividend accumulator making funds claimable by all token holders proportionally. The 5% goes to the protocol treasury. See [Dividend Distribution](dividends.md) for detailed accumulation mechanics.

## Access Verification

The IDO contract stores an access expiration timestamp for each researcher address: $$\text{accessExpiresAt}[researcher]$$. TEE environments query this on-chain value before granting dataset access. If the current block timestamp exceeds expiration, access is denied until the researcher purchases additional hours. This on-chain verification ensures payment enforcement without trusting TEE operators.
