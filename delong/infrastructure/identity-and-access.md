# Identity and access

_Author: Dylan, Avinasi Labs_

Access to encrypted datasets requires proof of identity and proof of on-chain rental. This page covers how wallet authentication works, how rental status is verified, and how the **Authorization Token** scopes access to specific datasets for a single job.

## Wallet authentication

Consumers authenticate by signing a challenge message with their wallet. The **Privacy Plane** verifies the signature against the wallet address and confirms it controls the address that holds the rental. No username, password, or API key is involved — the wallet is the sole identity credential.

The wallet private key stays on the consumer's device. It is never transmitted to the Privacy Plane or injected into a CVM.

## On-chain rental verification

After verifying the wallet signature, the Privacy Plane queries the Avinasi Protocol smart contracts to check whether the wallet holds an active rental for each requested dataset. The query calls `hasAccess(user)` on each dataset's **RentalOnly** contract. If any dataset returns false, the request is rejected.

This check runs at job submission time. A rental that expires mid-job does not interrupt execution — the Authorization Token was already issued and the DEKs were already delivered to the CVM.

## Authorization Token

The Privacy Plane issues a one-time **Authorization Token** after authentication and rental verification succeed. The token contains the wallet address, the list of authorized dataset IDs, a 128-bit random nonce, and the Privacy Plane's signature.

The token is scoped in three ways:

- **User scope**: bound to the authenticated wallet address.
- **Dataset scope**: lists only the datasets the user has active rentals for and requested in this job.
- **Time scope**: valid for a short window (e.g. 10 minutes from issuance).

The nonce prevents replay. Once a token's nonce is recorded as used, any second attempt to present the same token is rejected.

## Why not other methods

| Method | Problem |
|--------|---------|
| Wallet signing inside CVM | Exposes the private key to the TEE environment |
| On-chain authorization per CVM | Cannot bind a job ID to a specific CVM instance |
| Controller-issued guarantee | Requires trusting a centralized controller |

The Authorization Token avoids all three problems. The wallet signs only during the initial authentication step outside the CVM. The token binds authorization to a specific job without on-chain transactions per CVM. And the Privacy Plane's signature is verifiable without trusting any single operator.
