# Factory Contract

The Factory contract coordinates deployment of dataset contract suites using the EIP-1167 minimal proxy pattern, maintaining a counter of deployed datasets.

## Core Responsibilities

The Factory handles deployment coordination, maintains a dataset counter for unique IDs, and manages implementation contract addresses that proxies delegate to. Implementation upgrades affect future deployments only, never existing datasets.

## Deployment Function

Projects call `deployIDO` to launch a new dataset:

**Parameters:**
- `projectAddress` - Project owner receiving contract ownership
- `name` - Token name (e.g., "Longevity Data Token")
- `symbol` - Token symbol (e.g., "LDT")
- `metadataURI` - IPFS CID pointing to dataset metadata JSON
- `rentalPricePerHour` - Rental price in USDC (6 decimals)
- `config` - IDOConfig struct containing:
  - `rTarget` - Funding goal in USDC (6 decimals)
  - `alpha` - Project ownership ratio in basis points (1-5000 = 0.01%-50%)

**Returns:**
- `datasetId` - Sequential counter (1, 2, 3...)

### Parameter Validation

The Factory enforces protocol-wide constraints:

**Funding goal**: Must be greater than zero. No minimum or maximum limits exist.

**Alpha coefficient**: Must be between 1-5000 basis points (0.01%-50%). Determines team allocation relative to total supply.

**Metadata requirement**: Cannot be empty string. Should contain IPFS CID or URL pointing to dataset description.

### Supply Calculation

The Factory calculates total token supply using the Virtual AMM library:

$$
S_{total} = \frac{R_{target}}{P_0} \cdot \sqrt{\frac{\alpha}{(1-\alpha)^3}}
$$

Where $$P_0 = 0.01$$ USDC (initial price). This deterministic formula ensures consistent token economics across datasets.

## Minimal Proxy Pattern

The Factory uses OpenZeppelin's Clones library implementing EIP-1167 for gas-efficient deployment.

### Implementation Contracts

One-time deployment of full contract bytecode:
- DatasetToken implementation
- IDO implementation
- Governance implementation
- RentalPool implementation

These implementations are set in the Factory constructor and can be upgraded for future deployments.

### Proxy Creation

Each dataset deployment clones four minimal proxies (~200 bytes each). The proxy bytecode delegates all calls to the implementation contract, saving ~98% gas compared to deploying full contracts.

### Gas Comparison

Traditional deployment: ~12M gas (~\$1,200 at 50 gwei, \$2,000 ETH)
Minimal proxy: ~200K gas (~\$20 at same conditions)

## Contract Initialization

Proxies initialize separately from deployment since they lack constructors.

### Initialization Order

The Factory initializes contracts in dependency order:

1. **RentalPool** - Needs USDC, token, and IDO addresses
2. **DatasetToken** - Needs name, symbol, owner, IDO, and pool addresses; mints entire supply to IDO
3. **Governance** - Needs IDO, USDC, and Uniswap router/factory addresses
4. **IDO** - Needs all other addresses; receives minted tokens

Each contract has an `_initialized` flag preventing re-initialization attacks.

## Dataset Registry

The Factory maintains only a counter (`datasetCount`) for generating sequential dataset IDs. No on-chain registry array exists—indexing is done through events.

### IDOCreated Event

Emitted for each deployment with:
- `datasetId` - Sequential ID
- `projectAddress` - Project owner
- `ido` - IDO contract address
- `token` - DatasetToken address
- `governance` - Governance address
- `pool` - RentalPool address
- `virtualUsdc` - Initial virtual USDC reserve
- `virtualTokens` - Initial virtual token reserve

Off-chain systems index these events to build dataset catalogs.

## Configuration

Before deploying any IDO, the owner must call `configure` once to set:
- `feeTo` - Protocol fee recipient address
- `uniswapV2Router` - Uniswap V2 Router address
- `uniswapV2Factory` - Uniswap V2 Factory address

This one-time configuration cannot be changed afterward.

## Ownership

The Factory retains ownership of DatasetToken and RentalPool contracts. This is intentional—project addresses should not control token minting or dividend distribution. The IDO and Governance contracts handle project operations within protocol-defined constraints.

## Implementation Upgrades

The Factory owner can update implementation addresses. This affects **only future deployments**, never existing datasets. Each deployed proxy permanently delegates to the implementation address set at its deployment time.

Upgrading enables:
- Bug fixes for new projects
- Feature additions without affecting existing datasets
- Gas optimizations for future deployments

Without:
- Affecting launched projects
- Requiring migrations
- Breaking deployed contracts

## Security Considerations

**Immutable deployments** - Once initialized, proxies cannot change their implementation reference or re-initialize with different parameters.

**Factory ownership** - The Factory owner (typically multisig) can upgrade implementations but cannot affect existing datasets.

**Initialization protection** - The `_initialized` flag prevents anyone from re-initializing deployed contracts.

**Sequential IDs** - The counter ensures unique dataset identification without collision risk.

## Example Deployment

Project calls Factory:

```solidity
factory.deployIDO(
    0x123...abc,                    // projectAddress
    "LongevityData",                // name
    "LDATA",                        // symbol
    "ipfs://Qm...xyz",              // metadataURI
    12e6,                           // 12 USDC/hour
    {
        rTarget: 50_000e6,          // 50,000 USDC goal
        alpha: 2000                 // 20% team allocation
    }
);
```

Factory:
1. Increments datasetCount to generate ID
2. Calculates total supply using Virtual AMM formula
3. Clones 4 minimal proxies (~200K gas)
4. Initializes all contracts in dependency order
5. Emits IDOCreated event with all addresses

Result: Fully operational dataset ready for fundraising at ~\$20 deployment cost.
