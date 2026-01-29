# Two-plane architecture

_Author: Dylan, Avinasi Labs_

Avinasi separates key management from workload execution. The **Privacy Plane** holds secrets and enforces access policy. The **Computing Plane** runs consumer code against encrypted data. Neither plane trusts the other — they communicate through attestation-verified channels.

## Privacy Plane

The **Privacy Plane** runs on Phala dstack, a multi-node high-availability TEE cluster. It has three responsibilities:

- Derive and store per-dataset **Data Encryption Keys** (DEKs) using a root Key Encryption Key (KEK). DEKs never leave TEE memory in plaintext.
- Authenticate consumers by verifying wallet signatures and querying on-chain rental status.
- Issue one-time **Authorization Tokens** that grant a specific CVM instance the right to request DEKs for a specific set of datasets.

The Privacy Plane does not execute consumer workloads. It does not see consumer code or consumer output.

## Computing Plane

The **Computing Plane** runs on Google Confidential VMs backed by Intel TDX (C3 machine series). Each Secure Mode job gets an independent CVM that is destroyed after completion. Explore Mode jobs run in ordinary Kubernetes pods with desensitized data.

Inside a Secure Mode CVM, a two-layer FUSE stack provides transparent decryption: JuiceFS fetches encrypted chunks from S3, and a **Decrypt FUSE** layer decrypts them in memory using DEKs obtained from the Privacy Plane. Consumer code reads plaintext from a standard POSIX path.

## Trust model

The two planes establish trust through remote attestation and ephemeral key exchange:

1. The CVM generates a TDX attestation report that binds an ephemeral ECDH public key to its hardware measurements.
2. The Privacy Plane verifies the attestation report, confirms the Authorization Token, and encrypts DEKs with the ECDH shared secret.
3. The CVM decrypts the DEKs and loads them into Decrypt FUSE instances.

At no point does any party outside a TEE boundary see plaintext DEKs. The consumer's wallet private key never enters the CVM. The Privacy Plane never sees consumer code or output.

## Why two planes

A single TEE that manages both keys and workloads would couple availability requirements. The Privacy Plane must be always-on (consumers need to start jobs at any time). The Computing Plane is ephemeral by design (CVMs spin up and tear down per job). Separating them also limits blast radius: compromising a CVM exposes only the DEKs for that job's datasets, not the root KEK.
