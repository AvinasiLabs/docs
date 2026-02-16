# Key distribution

_Author: Dylan, Avinasi Labs_

Getting keys from `cced` into a Computing Plane CVM requires proof of identity, proof of authorization, proof that the CVM is genuine, and a secure channel between them. This page traces the full path from wallet authentication to DEK delivery.

## Wallet authentication

Consumers authenticate using EIP-4361 (Sign-In with Ethereum). The consumer's browser signs a challenge message with the wallet's private key (`personal_sign`, EIP-191). `cced` recovers the public key from the signature via `ecrecover` on the secp256k1 curve and derives the wallet address — no need to know the public key in advance. No username, password, or API key is involved.

The wallet private key stays on the consumer's device. It is never transmitted to `cced` or injected into a CVM.

## On-chain rental verification

After verifying the wallet signature, `cced` queries the Avinasi Protocol smart contracts to check whether the wallet holds an active rental for each requested dataset. The query calls `hasAccess(user)` on each dataset's contract. If any dataset returns false, the request is rejected.

This check runs at job submission time. A rental that expires mid-job does not interrupt execution — the Job Credential was already issued and the keys were already delivered to the CVM.

## Job Credential

`cced` issues a one-time **Job Credential** after authentication and rental verification succeed. The credential binds the wallet address to a set of dataset IDs and carries `cced`'s signature.

The credential is scoped in three dimensions:

- **User scope**: bound to the authenticated wallet address.
- **Dataset scope**: lists only the datasets the user has active rentals for and requested in this job.
- **Time scope**: valid for a short window (e.g. 10 minutes from issuance).

A 128-bit random nonce prevents replay — once a credential's nonce is recorded as used, any second attempt is rejected.

**Why not other methods?** Having the wallet sign inside the CVM would expose the private key to the TEE environment. On-chain authorization per CVM cannot bind a job ID to a specific CVM instance. A centralized controller guarantee requires trusting a single operator. The Job Credential avoids all three: the wallet signs only during initial authentication outside the CVM; the credential binds authorization to a specific job without on-chain transactions per CVM; and `cced`'s signature is verifiable without trusting any single operator.

**Why it is not a redundant round-trip**: the CVM's host machine is untrusted. The Job Credential is injected into the CVM at boot, but because it carries `cced`'s cryptographic signature, the host cannot forge or modify it. TEE attestation alone proves code integrity ("this is a legitimate image"), not user identity ("this CVM acts on behalf of wallet 0x123"). The credential bridges that gap.

## Remote Attestation

After the CVM boots, `tee-agent` generates an ephemeral ECDHE keypair and a `request_id` (128-bit random number). It then produces a TDX attestation report whose `REPORTDATA` field contains a hash of the ephemeral public key and the `request_id`, binding both to the CVM's hardware measurements.

`tee-agent` sends three items to `cced`: the Job Credential, the attestation report, and the ephemeral public key.

The `request_id` prevents replay — even if an attacker captures the full request, resubmitting it fails because `cced` marks each `request_id` as consumed. Binding the public key into `REPORTDATA` (which is covered by the hardware signature) prevents MITM attacks — an attacker cannot substitute a different public key without invalidating the attestation report.

## ECDHE key exchange

`cced` performs five verification steps in sequence:

1. Verify the credential's signature is valid.
2. Verify the credential has not expired.
3. Verify the nonce has not been used before.
4. Verify the TDX attestation report against known-good measurements.
5. Verify the ephemeral public key matches the hash in `REPORTDATA`.

If all checks pass, `cced`'s Key Manager derives the requested DEKs and a REK for the job. `cced` computes an ECDHE shared secret from its own private key and the CVM's ephemeral public key, encrypts the keys with the shared secret, and returns the ciphertext.

`tee-agent` computes the same shared secret from its ephemeral private key and `cced`'s public key, decrypts the keys, and loads each DEK into its corresponding Decrypt FUSE instance via DecryptFS. The REK is held in memory for encrypting job output.

Because both sides use ephemeral keys, the channel provides forward secrecy — compromising long-term keys after the fact cannot decrypt past sessions.

## Security properties

| Property | Mechanism |
|----------|-----------|
| Wallet private key never enters CVM | Wallet signs only during initial authentication outside the CVM |
| Credential is single-use | Nonce recorded as consumed; replay rejected |
| Request is single-use | `request_id` marked as used; duplicate rejected |
| Channel is ephemeral and forward-secret | ECDHE with per-session keypairs |
| Public key bound to hardware | `REPORTDATA` covered by TEE hardware signature |
| Keys vanish after job | CVM destroyed; ephemeral keys and DEKs cleared from memory |
