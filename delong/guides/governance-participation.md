# Governance Participation

Guide for creating proposals, voting, and participating in protocol governance.

## Proposal Requirements

**Creation Threshold**: Hold ≥1% of total supply at `block.number - 1`

**Voting Period**: 7 days mandatory window

**Quorum**: >50% of total supply must vote FOR to pass

**Execution**: Anyone can call `executeProposal()` after voting ends

## Proposal Types

### Funding Proposals

Withdraw USDC from treasury for operational expenses.

**Parameters**:
- `amount`: USDC amount (6 decimals)
- `recipient`: Receiving address (typically project multisig)
- `description`: Budget justification with IPFS link to detailed breakdown

**Creation**:
```solidity
Governance.createFundingProposal(
    50000e6,                    // 50,000 USDC
    projectMultisig,
    "Q1 2024 operational budget: ipfs://Qm..."
)
```

**Evaluation Criteria**:
- Is budget itemized and justified?
- Does team track record support request?
- Is treasury runway sustainable?
- Are expenses aligned with project goals?

### Pricing Proposals

Adjust dataset rental hourly rate.

**Parameters**:
- `newPrice`: New hourly rate in USDC (6 decimals)
- `description`: Rationale for rate change

**Creation**:
```solidity
Governance.createPricingProposal(
    15e6,  // $15/hour
    "Increase rate based on high utilization and market demand"
)
```

**Evaluation Criteria**:
- How does rate compare to similar datasets?
- What's the demand elasticity?
- Will rate change maximize revenue or adoption?

### Delist Proposals

Shut down project and enable capital recovery.

**Parameters**:
- `description`: Reasoning for shutdown

**Creation**:
```solidity
Governance.createDelistProposal(
    "Project abandoned - 6 months no development, enable capital recovery"
)
```

**Execution Effects**:
1. Removes Uniswap liquidity (burns LP tokens)
2. Burns recovered dataset tokens
3. Combines recovered USDC + treasury = refund pool
4. Records supply snapshot
5. Enables `claimDelistRefund()` for proportional refunds

**Recovery Rate**: 40-90% depending on treasury usage and accumulated LP fees

See [protocol/governance/delisting.md](../protocol/governance/delisting.md) for details.

### Governance Upgrade Proposals

Replace voting strategy with external contract.

**Parameters**:
- `newStrategy`: Address implementing IGovernanceStrategy
- `description`: Explanation of new voting mechanism

**Creation**:
```solidity
Governance.createGovernanceUpgradeProposal(
    quadraticVotingContract,
    "Upgrade to quadratic voting for better representation"
)
```

**Use Cases**: Quadratic voting, conviction voting, delegation, or other governance innovations

## Voting Process

**Step 1: Review Proposal**

Query proposal details:
```solidity
Governance.proposals(proposalId)
```

Check snapshot block, voting deadline, current votes, and description.

**Step 2: Cast Vote**

```solidity
Governance.castVote(proposalId, support)
// support: true = FOR, false = AGAINST
```

**Voting Power**: Determined by `getPastVotes(voter, proposal.snapshotBlock)`

**Restrictions**:
- Can only vote once per proposal
- Must have held tokens at snapshot block
- Must vote before `endTime`

**Step 3: Monitor Results**

Track votes via `VoteCast` events:
```solidity
event VoteCast(
    address indexed voter,
    uint256 indexed proposalId,
    bool support,
    uint256 weight
)
```

## Execution

After voting period ends, anyone calls:

```solidity
Governance.executeProposal(proposalId)
```

**Quorum Check**:
$$
\text{Passes} = \frac{\text{forVotes} \times 10000}{\text{totalSupply}} \geq 5000
$$

If passed: Status = Executed, type-specific logic runs

If failed: Status = Defeated, no state changes

## Best Practices

**For Proposers**:
- Provide detailed budgets via IPFS for Funding proposals
- Engage community discussion before creating proposals
- Monitor similar proposals' outcomes
- Be responsive to token holder questions

**For Voters**:
- Read full proposal descriptions and linked materials
- Evaluate against project performance and track record
- Consider long-term sustainability over short-term gains
- Vote on all proposals to ensure quorum

**For Everyone**:
- Monitor proposal creation via `ProposalCreated` events
- Participate in off-chain discussions (forums, Discord)
- Build community consensus before formal voting
- Respect governance outcomes even if you disagree

## Emergency Scenarios

**Malicious Proposals**: 50% quorum requirement prevents small groups from executing harmful proposals while large holders inactive

**Proposal Spam**: 1% threshold limits proposal creation to significant stakeholders

**Voter Apathy**: If quorum consistently fails, consider governance strategy upgrade to lower threshold or implement delegation

See [protocol/governance/overview.md](../protocol/governance/overview.md) for complete governance model.
