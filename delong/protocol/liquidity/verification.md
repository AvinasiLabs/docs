# LP Lock Verification

The permanent lock property is cryptographically verifiable by anyone through on-chain queries and contract code review. No trust in project teams or third parties is required—investors can independently confirm LP custody and validate no removal backdoors exist.

## Verifying LP Custody

Anyone can confirm Governance holds 100% of LP tokens through four simple steps:

### Finding the LP Token Address

Query the Uniswap V2 Factory contract to get the LP token address for the dataset's trading pair. The factory maintains a registry of all pairs, returning the LP token contract address when queried with the two token addresses (dataset token + USDC).

### Checking Governance Balance

Query the LP token contract to see how many tokens the Governance contract holds. This shows the locked LP amount under governance custody.

### Checking Total LP Supply

Query the LP token contract's total supply function to see how many LP tokens exist in total.

### Verifying 100% Ownership

Compare the two values. If Governance balance equals total LP supply, then 100% of liquidity is locked under governance control.

### Etherscan Verification

Non-technical users can verify through Etherscan:

1. Navigate to the LP token contract address
2. Open the "Read Contract" tab
3. Call `balanceOf` with the Governance address
4. Call `totalSupply`
5. Verify the two values match

## Verifying No Removal Functions

Confirming LP custody is only half the verification. You must also verify **no functions exist** that could transfer LP tokens out of Governance.

### Reading Contract Source

Navigate to the Governance contract on Etherscan and view the verified source code. Unverified contracts cannot be audited and should be considered high-risk.

### Searching for LP Transfers

Search the code for patterns that could transfer LP tokens:

**Direct transfers**: Look for calls to the LP token's transfer function.

**Approve-then-call**: Look for approval grants followed by external contract calls that could execute transferFrom.

**Low-level calls**: Look for raw call or delegatecall operations that could invoke LP token functions indirectly.

### Verifying Context

For any occurrences found, verify they're within legitimate functions. The only acceptable LP transfer should be within delisting proposal execution:

**Legitimate example** – LP removal in delisting execution:
- Only callable through internal execution path
- Requires delisting proposal creation (1% threshold)
- Requires 50%+ token holder approval
- Requires 7-day voting period
- Permissionless execution after deadline

**Illegitimate example** – Emergency withdrawal function:
- Single admin can call
- No governance vote required
- Instant execution
- Clear rug pull vector

### Checking for Admin Privileges

Confirm the Governance contract has no privileged roles:

❌ No owner or admin variables
❌ No emergency withdrawal functions
❌ No pause mechanisms that could block delisting
❌ No upgrade patterns (proxy, delegatecall to external logic)

The only authority should be the governance vote mechanism itself.

## Verifying Delisting Requirements

The legitimate delisting path should have proper safeguards protecting against easy liquidity removal.

### Proposal Creation Threshold

Creating delisting proposals should require 1% of total token supply. This prevents spam proposals while remaining accessible to legitimate token holders.

### Voting Period

Proposals should have a mandatory 7-day voting period. This gives token holders time to review, discuss, and vote on delisting decisions.

### Quorum Requirement

Execution should require 50%+ of total supply voting yes. This ensures delisting represents true majority consensus, not just active voter preference.

### Permissionless Execution

After the voting period ends, anyone should be able to execute passed proposals. This prevents vote censorship where proposal creators could prevent execution of unwanted outcomes.

### Single Execution

Each proposal should execute at most once. This prevents double-execution attacks where the same passed proposal could be replayed multiple times.

## Comprehensive Verification Checklist

Use this checklist to thoroughly verify a dataset's LP locking:

### On-Chain State
- [ ] Query LP token address via Factory
- [ ] Confirm Governance balance equals LP total supply
- [ ] Verify LP token is legitimate Uniswap V2 pair
- [ ] Check pool reserves contain expected token amounts

### Contract Code
- [ ] Governance source code verified on Etherscan
- [ ] No owner/admin privileged roles exist
- [ ] No emergency withdrawal functions
- [ ] No upgrade mechanisms (contract is immutable)
- [ ] LP transfers only in delisting execution
- [ ] Delisting requires 50% quorum
- [ ] Delisting has 7-day voting period
- [ ] No timelock or delay blocking delisting

### Proposal System
- [ ] Creation requires 1% threshold
- [ ] Voting uses snapshot system (prevents flash loans)
- [ ] Quorum measures absolute percentage, not turnout ratio
- [ ] Execution is permissionless after deadline
- [ ] Double-voting prevention mechanism exists

### Historical Activity
- [ ] Review all past proposals and votes
- [ ] Confirm no suspicious governance activity
- [ ] Verify initial LP creation sent tokens to Governance
- [ ] Check no LP token transfers from Governance occurred

## Red Flags

Watch for these warning signs indicating potential issues:

### Code-Level Red Flags

🚩 **Owner/admin variables** – Privileged access could bypass all protections

🚩 **Upgrade mechanisms** – Proxy patterns could enable malicious contract logic replacement

🚩 **Emergency functions** – "Emergency" withdrawals are standard rug pull vectors

🚩 **Pausable modifiers** – Could brick legitimate delisting execution

🚩 **Complex delegatecall patterns** – Could hide LP transfer logic in external contracts

🚩 **Unverified source code** – Cannot audit what you cannot read

### State-Level Red Flags

🚩 **Governance owns <100% of LP** – Partial lock only, some liquidity not protected

🚩 **Multiple LP pools** – Additional liquidity pools not under Governance control

🚩 **Suspicious proposal history** – Failed attacks or concerning governance patterns

🚩 **Low quorum proposals passing** – Indicates quorum requirements not properly enforced

## Example Verification Walkthrough

Complete process for verifying LP custody:

### 1. Get Contract Addresses

From project documentation:
- DatasetToken: `0x123...abc`
- Governance: `0x456...def`
- USDC: `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48`

### 2. Find LP Token

Visit Uniswap V2 Factory on Etherscan (`0x5C69bEe701ef814a2B6a3EDD4B1652CB9cc5aA6f`), call `getPair` with dataset token and USDC addresses, receive LP token address.

### 3. Check Balances

Visit LP token contract:
- Query `balanceOf(Governance address)`: 125,000 LP tokens
- Query `totalSupply()`: 125,000 LP tokens
- ✅ Governance owns 100%

### 4. Review Contract Code

Visit Governance contract, view verified source:
- Search for "lpToken.transfer"
- Find only occurrence in delisting execution function
- Verify function requires 50% vote and 7-day period
- ✅ No backdoors found

### 5. Check Historical Activity

Review Governance events tab:
- Review all ProposalCreated events
- Review all VoteCast events
- Verify no suspicious patterns
- ✅ Clean governance history

### 6. Document Results

- LP Custody: ✅ Verified 100% locked
- No Backdoors: ✅ Verified no removal functions
- Proper Quorum: ✅ Verified 50% requirement
- Delisting Path: ✅ Verified 7-day + majority vote
- **Overall: Fully verified permanent lock**

Transparent verifiability is a core DeLong principle. Anyone can confirm LP custody and validate protocol claims match implementation reality through on-chain data and published source code. Unlike opaque multisig custody or closed-source solutions, DeLong's LP locking is fully auditable by anyone with basic blockchain literacy.
