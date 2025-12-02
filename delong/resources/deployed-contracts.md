# Deployed Contracts

_Author: Dylan, Avinasi Labs_

Official DeLong Protocol contract addresses across supported networks.

## Ethereum Mainnet

**Status**: Not yet deployed

## Sepolia Testnet

**Status**: Active development - contract addresses may change during testing phase

**Factory**: [`0x4d76e333118B7957901F480fd2EAEcf080A31408`](https://sepolia.etherscan.io/address/0x4d76e333118B7957901F480fd2EAEcf080A31408)

**Implementation Addresses**:
- IDO Implementation: [`0x92A1f6d4c37b11ac0e51361F26a73AC753a11968`](https://sepolia.etherscan.io/address/0x92A1f6d4c37b11ac0e51361F26a73AC753a11968)
- DatasetToken Implementation: [`0x30B43A8283695E869CdDc344FD000f1a8a28E5b1`](https://sepolia.etherscan.io/address/0x30B43A8283695E869CdDc344FD000f1a8a28E5b1)
- RentalPool Implementation: [`0xB423b0DC71bdace30cB6eF3806493e30C770CD72`](https://sepolia.etherscan.io/address/0xB423b0DC71bdace30cB6eF3806493e30C770CD72)

**External Dependencies**:
- Mock USDC: [`0x2bfD56D08d549544AA492Ca021fc3Eb959386Fc0`](https://sepolia.etherscan.io/address/0x2bfD56D08d549544AA492Ca021fc3Eb959386Fc0)
- Uniswap V2 Router: Check Sepolia Uniswap docs
- Uniswap V2 Factory: Check Sepolia Uniswap docs

**Protocol Configuration**:
- Fee Recipient: [`0x7C04CeE8A78e736e1A4f2Bc43E7CCbCB8E3a9114`](https://sepolia.etherscan.io/address/0x7C04CeE8A78e736e1A4f2Bc43E7CCbCB8E3a9114)
- Deployer: [`0x7C04CeE8A78e736e1A4f2Bc43E7CCbCB8E3a9114`](https://sepolia.etherscan.io/address/0x7C04CeE8A78e736e1A4f2Bc43E7CCbCB8E3a9114)

## Verification

All contracts are verified on Sepolia Etherscan. View Factory contract to find deployed dataset instances via `IDOCreated` events.
