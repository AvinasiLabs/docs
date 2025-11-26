# Voting Mechanics

Token holders vote on proposals during seven-day voting periods using snapshot-based voting power. The system prevents manipulation through historical balance lookups and one-vote-per-address enforcement.

## Snapshot Voting

Voting power derives from token balance at a historical block recorded when the proposal was created, not current balance. This snapshot mechanism prevents several attack vectors.

### Snapshot Block

When a proposal is created, the contract records `block.number - 1` as the snapshot block. Voting power for all participants is frozen at that historical moment.

### Power Calculation

$$
\text{Voting Power} = \text{balanceOf}(voter, snapshotBlock)
$$

A holder with 10,000 tokens at snapshot time can cast 10,000 votes, regardless of balance changes during voting.

### Attack Prevention

**Flash loan attacks** – Borrowing tokens to vote and returning them in same transaction fails because snapshot predates the loan.

**Vote-and-transfer** – Voting then transferring tokens to another address for additional votes fails because both addresses use the same historical snapshot.

**Last-minute accumulation** – Buying tokens after proposal creation doesn't increase voting power, preventing rushed accumulation to swing votes.

**Double-voting** – Transferring tokens between addresses after voting fails because hasVoted tracks addresses, not token movements.

## Vote Casting

Token holders call the vote function during the seven-day period:

### Parameters

- Proposal ID: Which proposal to vote on
- Support: True for yes, false for no

### Requirements

- Current time < deadline (7 days from creation)
- Address hasn't already voted on this proposal
- Address held tokens at snapshot block

### Vote Recording

Votes accumulate into two counters:
- votesFor: Total voting power supporting the proposal
- votesAgainst: Total voting power opposing the proposal

Once an address votes, subsequent attempts revert. Votes cannot be changed or withdrawn.

## Quorum Requirements

Proposals pass if yes votes exceed 50% of total token supply:

$$
\text{votesFor} > \frac{\text{totalSupply}}{2}
$$

This represents true majority support—not majority of participants, but majority of all tokens.

### Example Thresholds

| Total Supply | Required Yes Votes | Percentage |
|--------------|-------------------|------------|
| 1,000,000 | 500,001 | 50.0001% |
| 3,000,000 | 1,500,001 | 50.0001% |
| 10,000,000 | 5,000,001 | 50.0001% |

Against votes don't matter—only yes votes must cross 50%. A proposal with 600,000 yes and 300,000 no passes if total supply is 1,000,000.

### Non-Voting as Rejection

Tokens that don't vote effectively count as "no" because yes votes must exceed 50% of total supply, not just 50% of votes cast. High quorum requirements protect against:

- Small groups passing proposals while large holders inactive
- Low-engagement manipulation
- Rushed decisions without broad support

## Voting Period

All proposals have fixed seven-day voting windows from creation to deadline. No extensions, no early closures, no expedited execution.

### Timeline

- **Day 0**: Proposal created, snapshot block recorded
- **Days 1-7**: Voting period, anyone with voting power can cast vote
- **Day 7**: Deadline passes, voting closes
- **Day 7+**: Anyone can execute if passed

### Rationale

Seven days balances:
- Sufficient time for community review and discussion
- Advance warning about potential treasury withdrawals or pricing changes
- Coordination time for distributed token holders across timezones
- Not so long that urgent decisions face excessive delays

## Vote Delegation

The default voting system requires direct participation—token holders must personally sign vote transactions. Alternative governance strategies can implement delegation.

### Current Limitation

Token holders who cannot actively monitor governance must either:
- Check proposals regularly and vote
- Trust proposals will align with their interests
- Accept non-participation counts as "no"

### Future Upgrade Path

Governance Upgrade Proposals can adopt delegation-enabled strategies where:
- Token holders delegate voting power to representatives
- Representatives vote on behalf of multiple delegators
- Delegators can reclaim power or override specific votes

This requires passing a Governance Upgrade Proposal to switch to a delegation-supporting strategy contract.

## Vote Privacy

All votes are public on-chain. Anyone can query historical Vote events to see:
- Which address voted
- Which proposal they voted on
- Whether they voted yes or no
- How much voting power they used

This transparency enables:
- Community accountability for large holder decisions
- Analysis of voting patterns and whale behavior
- Verification that vote counts are accurate
- Detection of coordinated voting cartels

Private voting would require cryptographic schemes like commit-reveal, which the default strategy doesn't implement.

## Execution Requirements

After voting closes, anyone can call the execution function if requirements are met:

1. Voting deadline has passed
2. Yes votes > 50% of total supply
3. Proposal hasn't already been executed
4. Proposal-specific preconditions satisfied (e.g., sufficient treasury balance for Funding Proposals)

Execution is permissionless—any address can trigger it. This prevents censorship where proposers might refuse to execute unfavorable results.

## Vote Counting Edge Cases

### Exactly 50%

If yes votes equal exactly 50% of total supply (e.g., 1,500,000 of 3,000,000), the proposal fails. The requirement is strictly greater than 50%, not greater-than-or-equal.

### Burned Tokens

If tokens are burned (sent to address zero), total supply decreases, making quorum easier to reach. A project that burns 10% of supply only needs 50% of remaining supply to pass proposals.

### Zero No Votes

Proposals can pass with zero against votes if yes votes exceed 50% of supply. Against votes are recorded but don't affect passage calculation.

### Tied Supply

If somehow yes votes and total supply both equal zero (impossible in practice), the division would revert. Empty proposals cannot exist because creation requires 1% threshold.

## Gas Optimization

Vote transactions are gas-efficient:
- Single historical balance lookup
- Two storage writes (vote mapping, vote counter)
- Event emission

Typical vote costs ~50,000-70,000 gas, similar to ERC20 transfers.

Large holders with millions of tokens pay the same gas as small holders—voting power comes from balance, not vote transaction size.

## Voting Strategies Comparison

| Strategy | Current Default | Quadratic | Conviction | Delegation |
|----------|----------------|-----------|------------|------------|
| **Power formula** | Linear (1 token = 1 vote) | √tokens | tokens × time | Delegated + own |
| **Whale influence** | Proportional to holdings | Reduced via square root | Increased via locking | Concentrated in reps |
| **Participation** | Requires active voting | Requires active voting | Rewards long holders | Enables passive participation |
| **Complexity** | Simple | Moderate | High | Moderate |
| **Current status** | Active | Upgradeable | Upgradeable | Upgradeable |

Communities can switch strategies via Governance Upgrade Proposals if the current linear voting proves suboptimal.
