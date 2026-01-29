# Key distribution

_Author: Dylan, Avinasi Labs_

Getting a DEK from the **Privacy Plane** into a **Computing Plane** CVM requires three things: proof that the consumer is authorized, proof that the CVM is genuine, and a secure channel between them. This page covers the Authorization Token, the Remote Attestation step, and the ECDH key exchange that ties them together.

## Authorization Token

When a consumer submits a Secure Mode job, the Privacy Plane issues a one-time **Authorization Token**. The token binds a wallet address to a set of dataset IDs and includes a cryptographic nonce:

- `user`: the consumer's wallet address
- `datasets`: list of rented dataset IDs
- `nonce`: 128-bit random value (collision probability 2⁻¹²⁸)
- `issued_at`: Unix timestamp
- `signature`: Privacy Plane's signature over the above fields

Before issuing the token, the Privacy Plane verifies the consumer's wallet signature and queries the chain to confirm active rental status for each requested dataset.

## Remote Attestation

After the CVM boots, it generates an ephemeral ECDH keypair and produces a TDX attestation report. The report's `REPORTDATA` field contains a hash of the ephemeral public key, binding that key to the CVM's hardware measurements.

The CVM sends three items to the Privacy Plane: the Authorization Token, the attestation report, and the ephemeral public key.

## Verification and ECDH exchange

The Privacy Plane performs five checks in sequence:

1. Verify the token's signature is valid.
2. Verify the token has not expired (issued_at within a configurable window, e.g. 10 minutes).
3. Verify the nonce has not been used before.
4. Verify the TDX attestation report against known-good measurements.
5. Verify the ephemeral public key matches the hash in `REPORTDATA`.

If all checks pass, the Privacy Plane derives the requested DEKs, computes an ECDH shared secret from its own private key and the CVM's ephemeral public key, encrypts the DEKs with the shared secret, and returns the ciphertext.

The CVM computes the same shared secret from its ephemeral private key and the Privacy Plane's public key, decrypts the DEKs, and loads each one into its corresponding Decrypt FUSE instance.

## Security properties

The consumer's wallet private key never enters the CVM. The Authorization Token is single-use — replaying a nonce causes rejection. The ECDH channel is ephemeral and forward-secret. Compromising a CVM after the job ends yields nothing because the ephemeral keys are destroyed with the CVM.

## Nonce management

Used nonces are stored with their `issued_at` timestamp. A background process periodically deletes entries older than the token expiry window. Alternatively, each nonce can be stored in Redis with a TTL equal to the window duration.
