# Launching Datasets

Guide for tokenizing datasets and deploying Initial Data Offerings.

## Pre-Launch Preparation

**Dataset Requirements**:
- TEE-encrypted data storage with access control
- Metadata description (IPFS CID or URL)
- Pricing model for rental access
- Target market and use case definition

**Parameter Selection**:
- **rTarget**: Fundraising goal (e.g., 50,000 USDC)
- **Alpha**: Team allocation 1-5000 basis points (e.g., 2000 = 20%)
- **Rental Rate**: Initial hourly price (e.g., 10 USDC/hour)
- **Token Name/Symbol**: Dataset identifier

See [protocol/ido/parameters.md](../protocol/ido/parameters.md) for parameter optimization.

## Deployment Process

**Step 1: Call Factory.deployIDO()**

```solidity
function deployIDO(
    address projectAddress,    // Project multisig
    string memory name,        // "Longevity Dataset Alpha"
    string memory symbol,      // "LDA"
    string memory metadataURI, // "ipfs://Qm..."
    uint256 rentalPricePerHour,// 10e6 (10 USDC)
    IDOConfig memory config    // rTarget, alpha
)
```

**Step 2: Factory Actions**:
1. Calculates total supply using Virtual AMM formula
2. Deploys minimal proxies (IDO, Token, Governance, RentalPool)
3. Initializes contracts in dependency order
4. Emits `IDOCreated` event with all addresses

**Step 3: Monitor Launch**:
- Track fundraising progress via `getProgress()`
- Monitor `TokensPurchased` events
- Launch triggers automatically when all salable tokens sell

## Post-Launch Operations

**Treasury Management**:
- Create Funding Proposals for operational expenses
- Provide detailed budgets via IPFS
- Submit proposals quarterly with 7-day voting periods

**Rental Pricing**:
- Create Pricing Proposals to adjust hourly rates
- Monitor utilization and revenue trends
- Optimize based on demand elasticity

**Dataset Updates**:
- Call `updateMetadata(newMetadataURI)` for version updates
- Maintain metadata history on-chain
- Communicate changes to token holders

**Revenue Distribution**:
- Rental payments automatically flow to RentalPool (95%) and protocol (5%)
- Token holders claim dividends independently
- No manual processing required

## Governance Best Practices

**Transparent Operations**:
- Document all treasury spending with receipts
- Provide regular progress updates
- Build trust through consistent delivery

**Conservative Budgeting**:
- Request quarterly funding maintaining 6-12 month runway
- Account for proposal rejection risk
- Integrate rental revenue as it grows

**Community Engagement**:
- Participate in proposal discussions
- Respond to token holder questions
- Demonstrate responsible stewardship

See [guides/governance-participation.md](governance-participation.md) for proposal creation details.

## Emergency Scenarios

**Failed IDO**: If 100 days pass without selling all tokens, IDO enters Failed status. Token holders call `claimRefund()` for proportional USDC refunds.

**Delisting**: If project cannot continue, token holders can propose delisting to recover capital. See [protocol/governance/delisting.md](../protocol/governance/delisting.md).

## Technical Integration

**Smart Contract Addresses**: Retrieve from `IDOCreated` event or Factory registry

**Rental Access**: Integrate TEE verification querying `accessExpiresAt[user]` on-chain

**Event Monitoring**: Index `AccessPurchased`, `RentalDistributed`, and governance events

See [lab/overview.md](../lab/overview.md) for DeLong Lab integration (TEE environments, Python SDK, foundation models).
