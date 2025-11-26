# Delisting Mechanism

Failed or abandoned dataset projects can be formally shut down through delisting proposals, enabling proportional capital recovery for token holders. The process removes locked liquidity, combines recovered funds with treasury remainder, and distributes refunds based on token ownership.

## Delisting Rationale

Projects may require delisting for various failure modes:

**Team abandonment** – No updates, communication, or maintenance for extended periods (3+ months typically)

**Zero revenue** – Persistent absence of rental activity indicating no market demand for the dataset

**Dataset obsolescence** – Research advances render dataset outdated or superseded by superior alternatives

**Fraud discovery** – Dataset contains fabricated data, misrepresented quality, or violated terms

**Unsustainable economics** – Treasury exhausted with no viable path to profitability or continued operations

**Security compromise** – Dataset or infrastructure suffers irreparable security breach

## Proposal Creation

Any token holder with 1%+ supply can create a delisting proposal with justification for shutdown. The proposal follows standard governance flow—seven-day voting period, 50% quorum requirement.

### Justification

Delisting proposals typically include:
- Timeline of project failures or issues
- Evidence of abandonment or fraud
- Financial analysis showing unsustainability
- Community discussion summary
- Alternative data source recommendations for researchers

### Voting Considerations

Token holders evaluate whether delisting serves their interests better than continued operation:

**Favor delisting if**:
- Project clearly abandoned with no recovery path
- Treasury empty and no revenue to sustain operations
- Capital recovery exceeds expected future dividend value
- Dataset fraud or quality issues discovered

**Oppose delisting if**:
- Temporary difficulties may resolve
- Team is working on recovery plan
- Future rental revenue potential exceeds refund value
- Strategic value in maintaining project

## Execution Process

When a delisting proposal passes, the contract executes several steps to recover capital and enable claims:

### 1. Mark Delisted

The contract records `isDelisted = true`, preventing:
- New Funding Proposals
- Pricing changes
- Additional IDO purchases (if still in fundraising)
- Normal operations

### 2. Withdraw LP Tokens

LP tokens held in governance custody since launch are withdrawn from the permanent lock. These tokens represent the project's Uniswap pool ownership.

### 3. Remove Liquidity

The contract approves Uniswap Router to access LP tokens, then calls remove liquidity:

**Input**: LP token balance
**Output**: USDC amount + dataset token amount

The USDC includes:
- Original LP USDC from launch
- Accumulated trading fees (0.3% on all swaps)

The dataset tokens are returned but have limited value post-delisting.

### 4. Calculate Refund Pool

Total refundable USDC combines two sources:

$$
\text{Refund Pool} = \text{LP USDC} + \text{Treasury Balance}
$$

**LP USDC**: Recovered from Uniswap liquidity removal

**Treasury Balance**: Unspent operational funds remaining in governance

### 5. Enable Claims

The contract calculates per-token refund rate:

$$
\text{Refund Per Token} = \frac{\text{Refund Pool}}{\text{Total Token Supply}}
$$

Token holders can then claim their proportional share.

## Refund Claims

After delisting execution, token holders claim refunds by calling the claim function with their tokens.

### Claim Process

1. Token holder approves governance to transfer their tokens
2. Token holder calls claimRefund()
3. Contract transfers tokens from holder to itself (burn/lock)
4. Contract calculates refund: holder balance × refund per token
5. Contract transfers USDC refund to holder
6. Claim marked as completed to prevent double-claiming

Refund formula:

$$
\text{User Refund} = \text{Token Balance} \times \frac{\text{Total USDC}}{\text{Total Supply}}
$$

### Claim Timing

No deadline exists for claiming refunds. Token holders can claim months or years after delisting—USDC remains in the contract indefinitely.

Unclaimed refunds sit in the governance contract. No mechanism exists to sweep unclaimed funds—they remain available for rightful claimants permanently.

## Capital Recovery Analysis

### Recovery Components

Refund pools consist of two components with different characteristics:

**LP Recovery**:
- Initial LP USDC from launch (typically 40-60% of raise)
- Accumulated trading fees (0.3% on all secondary market volume)
- Generally predictable and substantial

**Treasury Remainder**:
- Unspent operational funds
- Varies widely based on project lifecycle
- Could be near-zero if treasury exhausted
- Could be substantial if delisting occurs early

### Recovery Rates

Typical recovery rates depend on project lifecycle stage:

**Early delisting** (team abandons immediately):
- Treasury: ~90-100% of initial deposit
- LP: ~100% of initial allocation
- **Total recovery: 80-90%** of raise

**Mid-lifecycle delisting** (some treasury spending):
- Treasury: ~30-50% of initial deposit
- LP: ~105-110% of initial (includes fees)
- **Total recovery: 50-70%** of raise

**Late delisting** (treasury exhausted):
- Treasury: 0-10% remaining
- LP: ~110-120% of initial (more fees accumulated)
- **Total recovery: 40-50%** of raise

Recovery rates depend on treasury usage and accumulated LP trading fees. Early abandonment with minimal treasury spending enables 90%+ recovery, while complete treasury exhaustion still yields 50%+ recovery due to permanent LP locking.

## Comparison with Traditional Failures

Traditional token projects typically offer zero capital recovery when teams abandon or projects fail. DeLong's permanent LP lock creates a recovery floor of 40-90% depending on treasury usage and timing, compared to 0% in traditional rugpulls or abandoned projects. The locked liquidity acts as collateral that cannot be removed by teams, ensuring capital recovery potential even in complete failure scenarios.

## Delisting as Investor Protection

The delisting mechanism provides exit liquidity when projects fail:

**Prevents indefinite capital lock** – Failed projects don't trap capital permanently; formal exit path exists

**Proportional distribution** – All token holders receive fair share based on ownership, no preferential treatment

**No trust required** – Execution is deterministic and verifiable; passing vote triggers automatic capital recovery

**Permanent LP guarantee** – Locked liquidity cannot be removed except via democratic delisting vote

**Democratic control** – Token holders collectively decide when project should shut down, not team unilaterally

**Trading fee recovery** – Accumulated fees from secondary trading enhance recovery beyond initial LP value

The mechanism transforms project failure from total loss into partial capital recovery, significantly reducing downside risk for dataset token investors.
