# Rental Payments

Researchers pay hourly fees in USDC to access datasets in TEE environments. Payments split 95% to token holder dividends and 5% to protocol fees, with automatic revenue distribution through the dividend pool.

## Rental Process

### Access Purchase

Researchers call the rental function on the IDO contract:

**Parameters**:
- Duration: Hours of access (e.g., 24 for one day)
- Payment: USDC amount = hours × hourly rate

**Result**:
- Access expiration timestamp extends by purchased hours
- USDC splits between dividend pool and protocol fees
- Revenue notification triggers dividend accumulator update

### Hourly Rate

Each dataset has an hourly rate in USDC (6 decimals) set by the project team at launch and adjustable via Pricing Proposals.

Typical rates range from \$5-50/hour depending on:
- Dataset size and completeness
- Data uniqueness and quality
- Competitive alternatives
- Target researcher demographics

### Access Duration

Access grants cumulative—purchasing additional hours extends existing access rather than replacing it:

- Current expiration: Block timestamp + 1,000,000 (11.5 days)
- Purchase: 240 hours (10 days)
- New expiration: Block timestamp + 1,000,000 + 864,000 = Block timestamp + 1,864,000 (21.5 days)

No maximum duration limit exists. Researchers can purchase years of access in a single transaction.

## Revenue Split

Each rental payment divides between two recipients:

$$
\text{Dividend Amount} = \text{Payment} \times 0.95
$$

$$
\text{Protocol Fee} = \text{Payment} \times 0.05
$$

### Dividend Flow

95% flows to the dividend pool (rental pool contract):

1. IDO transfers USDC to rental pool
2. Rental pool calls notifyRevenue
3. Accumulator updates: accRevenuePerToken += (amount × 1e18) / totalSupply
4. Token holders can claim updated pending amounts

### Protocol Fee

5% transfers to protocol fee recipient address configured at factory deployment. This supports protocol development, security audits, and operational costs.

## Access Verification

The IDO contract tracks access expiration per researcher address:

$$
\text{accessExpiresAt}[researcher]
$$

TEE environments query this timestamp before granting dataset access. If current block timestamp exceeds expiration, access is denied until additional rental purchase.

### TEE Integration

1. Researcher requests dataset access in TEE
2. TEE queries IDO contract: `accessExpiresAt[researcher]`
3. TEE compares with current block timestamp
4. If valid: Grant access
5. If expired: Reject with payment instructions

This on-chain verification ensures payment enforcement without trusting TEE operators.

## Pricing Adjustments

Token holders can modify hourly rates via governance:

### Pricing Proposal

1. Token holder creates Pricing Proposal with new rate
2. Seven-day voting period
3. If passed (50%+ yes votes), rate updates immediately
4. All subsequent rentals use new rate

Existing access periods honor the rate paid at purchase time—no retroactive price changes.

## Payment Requirements

### Sufficient Balance

Researchers must approve sufficient USDC for the IDO contract before calling rental function. Insufficient approval or balance causes transaction reversion.

### Minimum Purchase

No minimum purchase amount exists—researchers can buy single hours if desired. However, gas costs (~\$5-10) make very small purchases economically inefficient.

Practical minimum: 10-20 hours to amortize transaction costs.

### Maximum Purchase

No maximum limit. Researchers can purchase decades of access in one transaction if they have sufficient USDC and approval.

## Refund Policy

No refund mechanism exists. Purchased access hours are non-refundable and non-transferable.

Researchers should:
- Purchase conservatively initially
- Extend access as needed based on actual usage
- Avoid over-purchasing speculative long-term access

This no-refund policy ensures revenue stability for dividend distribution and prevents gaming of the rental system.
