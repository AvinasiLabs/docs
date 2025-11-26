# Proposal Types

The governance system supports four proposal types, each triggering different contract actions when passed. All follow the same voting process—seven-day period, 50% quorum, permissionless execution.

## Funding Proposals

Withdraw USDC from treasury to specified recipient addresses for operational expenses.

### Parameters

- Amount: USDC to withdraw (6 decimals)
- Recipient: Address receiving funds
- Description: IPFS hash linking to detailed budget

### Use Cases

Projects submit Funding Proposals for:
- Infrastructure costs (servers, storage, compute)
- Data acquisition (medical providers, research institutions)
- Team compensation (developers, data scientists, community managers)
- Marketing (conferences, content, researcher outreach)
- Upgrades (security, performance, scaling)

### Budget Documentation

Teams typically store detailed budgets on IPFS and reference the hash in the proposal description:

| Category | Line Item | Amount | Justification |
|----------|-----------|--------|---------------|
| Infrastructure | AWS hosting (3 months) | \$2,400 | \$800/month for compute + storage |
| Data | Medical provider partnership | \$3,000 | 5,000 additional patient records |
| Team | Part-time developer (3 months) | \$6,000 | Smart contract maintenance |
| Operations | Legal compliance review | \$1,500 | HIPAA/GDPR consultation |
| Buffer | Contingency (10%) | \$1,100 | Unexpected expenses |
| **Total** | | **\$15,000** | |

Token holders retrieve this document during voting to evaluate each line item against project needs and track record.

### Execution

When passed, the approved USDC amount transfers from governance treasury balance to the recipient address. No intermediate steps or additional approvals required.

## Pricing Proposals

Adjust the hourly rental rate stored in the IDO contract.

### Parameters

- New rate: USDC per hour (6 decimals)
- Description: Rationale for price change

### Use Cases

Pricing adjustments respond to:
- **Demand changes** – Increase rates when utilization high, decrease when adoption slow
- **Quality improvements** – Raise prices after major dataset expansions
- **Competitive positioning** – Adjust relative to alternative data sources
- **Revenue optimization** – Find price maximizing total rental income
- **Community access** – Temporarily lower for hackathons or research initiatives

### Execution

The IDO contract's hourly rate updates immediately. All subsequent rental purchases use the new rate.

## Delisting Proposals

Shut down failed or abandoned projects and enable proportional capital recovery.

### Parameters

- Description: Justification for delisting

### Use Cases

Communities initiate delisting for:
- **Team abandonment** – No updates or communication for 3+ months
- **Zero revenue** – Persistent zero rental activity indicating no demand
- **Dataset obsolescence** – Superseded by superior alternatives
- **Fraud discovery** – Fabricated data or misrepresented quality
- **Unsustainable economics** – Treasury exhausted with no profitability path

### Execution Process

When passed, the contract:

1. Marks dataset as delisted
2. Withdraws LP tokens from permanent lock
3. Removes liquidity from Uniswap
4. Combines recovered USDC with treasury remainder
5. Enables proportional refund claims for token holders

### Capital Recovery

The refund pool combines:
- LP recovery (original USDC + accumulated trading fees)
- Treasury remainder (unspent operational funds)

Token holders claim refunds proportional to their token balance:

$$
\text{Refund} = \text{Token Balance} \times \frac{\text{Total USDC Recovered}}{\text{Total Token Supply}}
$$

### Recovery Example

| Component | Amount |
|-----------|--------|
| Initial raise | \$50,000 |
| LP creation | \$25,000 |
| Treasury spending | -\$18,000 |
| Treasury remainder | \$7,000 |
| Trading fees accumulated | \$800 |
| LP recovery | \$25,800 |
| **Total refund pool** | **\$32,800** |
| **Recovery rate** | **65.6%** |

Even after spending \$18,000 and complete project failure, investors recover 65.6% of capital.

## Governance Upgrade Proposals

Switch voting logic to alternative strategy contracts implementing custom voting rules.

### Parameters

- New strategy address: Contract implementing IGovernanceStrategy
- Description: Rationale for upgrade

### Use Cases

Communities can adopt alternative governance models:

**Quadratic voting** – Voting power scales with square root of holdings, reducing whale influence

**Delegation systems** – Token holders delegate voting power to trusted representatives

**Conviction voting** – Time-weighted voting where longer locks increase power

**Liquid democracy** – Hybrid allowing both direct voting and delegation

**Optimistic governance** – Proposals pass unless opposed, reducing voter fatigue

### Execution

The governance strategy pointer updates to the new contract address. Future proposals use the new contract's vote counting and validation logic.

### Interface Requirements

Strategy contracts must implement three functions:
- `getVotingPower(address, uint256)` – Calculate voting power at snapshot
- `validateProposal(Proposal)` – Check proposal validity
- `calculateQuorum(uint256)` – Determine passing threshold

### Backward Compatibility

Core treasury custody, LP locking, and dividend distribution remain unchanged. Strategy upgrades only affect voting mechanics, not economic security properties.

## Creation Threshold

All proposal types require holding 1% of total token supply to create. This threshold:
- Prevents spam and noise attacks
- Allows legitimate minority concerns
- Permits 10-20 independent proposers in typical distributions

The threshold applies to token balance at proposal creation block, preventing flash loan attacks.

## Proposal Lifecycle

1. **Creation** – Proposer calls creation function with type-specific parameters
2. **Voting period** – 7 days for token holders to cast votes
3. **Deadline** – Voting ends, results finalize
4. **Execution** – Anyone calls execution if quorum reached
5. **Effect** – Type-specific logic executes atomically

No timelock exists between vote conclusion and execution—the seven-day voting period provides sufficient review time.
