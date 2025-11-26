# Treasury Custody

_Author: Dylan, Avinasi Labs_

The treasury holds USDC raised during the IDO for project operational expenses. Rather than existing as a separate contract, treasury functionality integrates directly into the Governance contract, creating mathematical guarantees about fund access that protect investor capital.

## Integrated Architecture

Traditional approaches deploy separate treasury contracts with independent access control. DeLong embeds treasury tracking as internal state within Governance, eliminating cross-contract attack vectors and simplifying security verification.

This integration provides investor protections:

**Single verification point** – Auditing one contract confirms both governance rules and treasury custody. No need to analyze interactions between multiple contracts or trust external treasury implementations.

**No cross-contract vulnerabilities** – Internal balance tracking eliminates reentrancy risks, delegatecall exploits, or interface confusion attacks possible when treasury exists as external contract.

**Atomic execution** – Treasury withdrawals execute in the same transaction context as proposal execution, preventing partial state updates or manipulation between operations.

**Deterministic access** – Only one code path can decrease treasury balance, making security analysis straightforward and comprehensive.

## Deposit Process

When an IDO completes successfully, the launch function automatically deposits treasury funds into Governance custody. The deposit originates directly from the IDO contract, ensuring funds enter governance control immediately upon launch without intermediate custody or team access.

### Deposit Restrictions

Only the associated IDO contract can deposit funds. This restriction prevents:

- Arbitrary deposits that could confuse accounting
- Front-running or deposit manipulation
- Injection of funds from unknown sources

The deposit amount matches the capital distribution formula from launch—total raised minus LP allocation equals treasury deposit.

## Withdrawal Requirements

Treasury withdrawals require satisfying five sequential conditions enforced through smart contract logic:

### 1. Proposal Creation (1% Threshold)

Creating a Funding Proposal requires holding at least 1% of total token supply. This threshold prevents spam proposals while remaining accessible to legitimate stakeholders. In typical distributions, early investors and team members easily exceed this threshold.

### 2. Seven-Day Voting Period

All proposals enter a mandatory seven-day voting window. No expedited execution exists. This period enables:
- Community review of detailed budgets
- Discussion of spending priorities
- Evaluation against project performance
- Coordination among distributed token holders

### 3. Majority Approval (50% Quorum)

Proposals require yes votes from over 50% of total token supply to pass (see [voting.md](voting.md) for complete voting mechanics). High quorum prevents small groups from accessing treasury while large holders are inactive.

### 4. Execution Call

After the voting deadline passes, anyone can call the execution function for passed proposals. This permissionless design means proposals cannot be censored—if voting conditions are met, execution will succeed regardless of who submits the transaction.

### 5. Sufficient Balance

The treasury must contain adequate USDC to cover the requested amount. Insufficient balance causes execution to fail, preventing proposals from over-drafting the treasury.

## Mathematical Enforcement

No admin keys, emergency withdrawals, or bypass mechanisms exist. The contract enforces these requirements through code logic that physically prevents treasury access outside the democratic process.

Anyone can verify withdrawal restrictions by examining the verified contract source code and confirming that only the proposal execution function can decrease treasury balance, and only when all voting requirements are satisfied.

## Treasury Operations

### Typical Funding Cycle

Projects submit Funding Proposals periodically as operational expenses arise:

1. **Budget preparation** – Team compiles detailed budget with line items (infrastructure, data acquisition, development, etc.)
2. **Proposal creation** – Team member with 1%+ holdings submits proposal with USDC amount, recipient address, and IPFS link to detailed budget
3. **Community review** – Token holders retrieve budget from IPFS and evaluate spending justification
4. **Voting** – Seven-day period for casting votes based on budget assessment
5. **Execution** – If passed, anyone triggers execution to transfer funds

### Evaluation Criteria

Token holders assess Funding Proposals based on:

**Project performance** – Is rental revenue growing? Are dataset updates regular? Is infrastructure maintained?

**Budget justification** – Are line items reasonable? Are there more cost-effective alternatives? Is spending aligned with stated priorities?

**Team track record** – Were previous funds used appropriately? Is spending transparent? Does the team deliver on commitments?

**Treasury runway** – How long will remaining treasury last? Is the request sustainable given current revenue?

## Comparison with Multisig

Traditional projects often use multisig wallets where a small group controls treasury access:

| Aspect | Multisig (3-of-5) | Governance (50% token vote) |
|--------|-------------------|---------------------------|
| **Decision visibility** | Off-chain, opaque | Fully on-chain, transparent |
| **Approval threshold** | 3 specific signers | 50% of all token holders |
| **Power distribution** | Equal among 5 people | Proportional to capital at risk |
| **Compromise difficulty** | Coerce 3 individuals | Acquire 50% of circulating supply |
| **Advance warning** | Instant execution | 7-day mandatory review period |
| **Stakeholder representation** | 5 selected individuals | All token holders proportionally |

### Governance Advantages

**Higher attack cost** – Acquiring 50% of circulating supply costs far more than compromising 3 multisig signers through coercion, bribery, or social engineering.

**Proportional influence** – Large investors who contributed more capital have correspondingly more influence over spending decisions, aligning incentives properly.

**Complete transparency** – All proposals, detailed budgets, votes, and execution are permanently on-chain and auditable by anyone.

**Mandatory review period** – Seven days prevents surprise treasury raids that are possible with instant multisig execution.

**No trust required** – Mathematical enforcement through code rather than trusting signer honesty or competence.

## Investor Protection Summary

The integrated treasury architecture protects investors through:

1. **Immediate custody** – Funds enter governance control at launch without team access
2. **Mathematical enforcement** – Code logic physically prevents unauthorized withdrawals
3. **Democratic control** – Majority approval required for all spending
4. **Complete transparency** – All proposals, budgets, votes, and executions on-chain
5. **Advance warning** – Seven-day review period for all withdrawals
6. **No backdoors** – No admin keys, emergency access, or bypass mechanisms
7. **Single verification** – Auditing one contract confirms complete custody model

Treasury custody relies on mathematical guarantees enforced by immutable contract code rather than trust in project teams or governance participants.
