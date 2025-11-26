# Security

_Author: Dylan, Avinasi Labs_

Security practices, audit reports, and vulnerability disclosure for DeLong Protocol.

## Audit Status

**Status**: Pending

**Planned Auditors**: TBD

**Audit Scope**:
- Factory.sol
- IDO.sol
- DatasetToken.sol
- Governance.sol
- RentalPool.sol
- VirtualAMM.sol (library)

**Audit Reports**: Available after completion

## Security Features

### No Admin Keys

Zero privileged roles or admin functions across all contracts. No emergency withdrawals, no pause mechanisms, no upgrade paths after initialization.

### Immutable Deployment

EIP-1167 minimal proxies with one-time initialization. No upgradeable proxy patterns or delegatecall vulnerabilities.

### Snapshot Voting

ERC20Votes checkpoints prevent flash loan attacks and double voting via historical balance queries at `block.number - 1`.

### Reentrancy Protection

ReentrancyGuard applied to all state-changing functions in IDO, Governance, and RentalPool contracts.

### Slippage Protection

User-specified limits on all swap functions:
- `maxUSDCIn` / `minTokensOut` for buying
- `maxTokensIn` / `minUSDCOut` for selling

### Decimal Safety

Explicit decimal handling with comments throughout codebase:
- USDC: 6 decimals
- Dataset tokens: 18 decimals
- Dividend precision: 1e18 scaling

### Initialization Guards

`_initialized` flags prevent re-initialization attacks on cloned minimal proxies.

## Known Limitations

### No Governance Upgrades Post-Launch

Governance strategy can only be changed via GovernanceUpgrade proposals (requires 50% quorum). Initial strategy is immutable simple majority.

### No Emergency LP Unlock

LP tokens locked permanently except via democratic delisting. No circuit breakers or emergency unlocks exist.

### Rounding Dust

Small rounding errors possible in dividend distribution due to fixed-point arithmetic. Dust amounts (<0.000001 USDC) may accumulate in RentalPool.

## Bug Bounty

**Status**: TBD

**Scope**: All core protocol contracts

**Rewards**: TBD

**Exclusions**:
- Frontend/UI bugs
- Gas optimizations
- Known limitations listed above

**Disclosure**: `security@delong.xyz` (email TBD)

## Responsible Disclosure

If you discover a security vulnerability:

1. **Do not** publicly disclose the issue
2. Email details to `security@delong.xyz`
3. Include steps to reproduce, impact assessment, and suggested fix
4. Allow 90 days for patch development and deployment
5. Coordinated disclosure after fix deployment

## Security Best Practices

### For Users

**Verify Contracts** - Always check contract addresses on [deployed-contracts.md](deployed-contracts.md) before interacting.

**Approve Carefully** - Only approve exact amounts needed for transactions, not unlimited approvals.

**Check Slippage** - Use appropriate slippage limits during volatile market conditions.

**Verify Proposals** - Review proposal details and discussion before voting, especially for large treasury withdrawals.

### For Developers

**No Private Keys in Code** - Never commit private keys, mnemonics, or API keys.

**Test Thoroughly** - Run full test suite before deploying integrations.

**Monitor Events** - Listen to contract events for unexpected state changes.

**Handle Reverts** - Implement proper error handling for all contract calls.

## Contact

Security inquiries: `security@delong.xyz` (TBD)

General support: See [FAQ](faq.md)
