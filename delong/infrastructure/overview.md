# Infrastructure

_Author: Dylan, Avinasi Labs_

DeLong infrastructure lets data providers monetize sensitive datasets without exposing raw records. Consumers run arbitrary code against that data and receive verified results. The system relies on hardware-enforced confidential computing rather than legal agreements or access-control lists.

## What providers get

Datasets are encrypted with a per-dataset key before they leave the provider's machine. At rest the ciphertext sits in object storage. In transit it travels over TLS. In memory it is decrypted only inside a Trusted Execution Environment whose integrity can be verified by remote attestation. Authorization checks run on-chain: a consumer must hold an active rental before the **Privacy Plane** releases any decryption material.

## What consumers get

Consumers write ordinary Python against a POSIX filesystem. No SDK lock-in, no API wrappers. A two-stage workflow separates exploration from production: **Explore Mode** offers interactive Jupyter access to desensitized samples, while **Secure Mode** runs batch jobs against full encrypted data inside an isolated Confidential VM. Every output passes through the **Output Gate** before it reaches the consumer.

## What developers get

The architecture splits into two TEE planes. The **Privacy Plane** (Phala dstack) manages key derivation, wallet authentication, and on-chain rental verification. The **Computing Plane** (Google Confidential VM, Intel TDX) executes consumer workloads in ephemeral CVMs that are destroyed after each job. Per-dataset Data Encryption Keys flow from Privacy Plane to Computing Plane over an ECDH channel established after remote attestation succeeds.

## Architecture at a glance

The [architecture walkthrough](architecture.md) explains how these components connect end-to-end. The pages that follow cover each layer in detail:

- [Two-plane architecture](two-plane-architecture.md) — Privacy Plane and Computing Plane separation
- [Compute modes](compute-modes.md) — Explore Mode and Secure Mode
- [Data encryption](data-encryption.md) — per-dataset DEK, JuiceFS, Decrypt FUSE
- [Key distribution](dek-distribution.md) — Authorization Token, Remote Attestation, ECDH
- [Output Gate](output-gate.md) — network isolation, automated detection, human review
- [Algorithm auditability](algorithm-auditability.md) — DA layer, image digest, reproducible builds
- [TEE foundations](tee-foundations.md) — Confidential computing, TDX, SEV-SNP, attestation
- [Identity and access](identity-and-access.md) — wallet auth, on-chain rental verification, token scoping
