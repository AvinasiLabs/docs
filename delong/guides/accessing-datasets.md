# Accessing Datasets

Guide for researchers purchasing dataset access and integrating into workflows.

## Discovering Datasets

**On-Chain Registry**:
- Query Factory contract for `IDOCreated` events
- Filter by metadata categories and pricing
- Check rental rates via `IDO.hourlyRate`

**Metadata Review**:
- Call `getMetadataHistory()` for dataset description
- Evaluate use cases, data quality, and update frequency
- Review governance activity and community engagement

## Purchasing Access

**Step 1: Approve USDC**

```solidity
USDC.approve(idoAddress, amount)
```

**Step 2: Purchase Hours**

```solidity
IDO.purchaseAccess(hoursCount)
```

Cost calculation: `hoursCount × hourlyRate`

**Step 3: Verify Access**

```solidity
uint256 expiresAt = IDO.accessExpiresAt(yourAddress)
// Returns timestamp when access expires
```

**Access Extensions**: Call `purchaseAccess()` again to extend expiration (cumulative).

## Revenue Distribution

**Automatic Split**:
- 95% → RentalPool (distributed to token holders as dividends)
- 5% → Protocol fee recipient

**No Refunds**: Purchased access is non-refundable and non-transferable. Purchase conservatively and extend as needed.

## Using DeLong Lab

**TEE-Secured Jupyter Environments**:

DeLong Lab provides confidential computing environments where researchers can access tokenized datasets without exposing raw data.

**Workflow**:
1. Connect wallet to DeLong Lab
2. Select dataset from registry
3. Lab queries `accessExpiresAt[yourAddress]` on-chain
4. If valid access, TEE grants dataset decryption keys
5. Run analysis in Jupyter notebook with Python SDK
6. Export results (not raw data)

**Python SDK**:

```python
from delong import DatasetClient

# Initialize with wallet and dataset address
client = DatasetClient(
    wallet_address="0x...",
    ido_contract="0x..."
)

# Verify access (queries on-chain)
if client.has_access():
    # Load dataset in TEE environment
    df = client.load_dataset()

    # Run analysis
    results = analyze(df)

    # Export results
    client.export_results(results)
```

**Longevity Foundation Models**:

Pre-trained models for aging biology, healthspan analysis, and therapeutic discovery available in Lab environment.

See [lab/overview.md](../lab/overview.md) for complete Lab documentation.

## Access Best Practices

**Cost Optimization**:
- Estimate analysis time before purchasing
- Start with 10-20 hours for initial exploration
- Extend access based on actual usage patterns

**Security**:
- Verify IDO contract address on [deployed-contracts](../resources/deployed-contracts.md)
- Never share TEE access credentials
- Export only aggregated results, not raw data

**Rate Changes**:
- Monitor governance proposals for pricing changes
- New rates apply only to future purchases
- Existing access honored at rate paid

## Integration Patterns

**Off-Chain Verification**:

Your application should query `accessExpiresAt[user]` before granting dataset access:

```solidity
function hasAccess(address user) external view returns (bool) {
    return block.timestamp < IDO.accessExpiresAt(user);
}
```

**Event Monitoring**:

Listen to `AccessPurchased` events to track researcher activity:

```solidity
event AccessPurchased(
    address indexed user,
    uint256 hoursCount,
    uint256 cost,
    uint256 newExpiresAt
);
```

**Batch Access**: Purchase access for multiple users by calling `purchaseAccess()` multiple times (no batch function exists).

## Troubleshooting

**Access Expired**: Call `purchaseAccess()` again to extend

**Insufficient USDC**: Approve more USDC for IDO contract

**Rate Changed**: Check current `hourlyRate` and recalculate cost

**TEE Access Denied**: Verify on-chain access hasn't expired

See [FAQ](../resources/faq.md) for common issues.
