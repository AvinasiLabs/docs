# Infrastructure

_Author: Dylan, Avinasi Labs_

Avinasi infrastructure lets data providers monetize desensitized datasets while keeping raw records private. Consumers run arbitrary code against that data and receive verified results.

## Trust model

Data is end-to-end encrypted from the provider's machine into the TEE. Avinasi never sees plaintext at any point during upload, storage, or transit.

The logic inside the TEE is hardcoded: it only responds to on-chain requests. No one — not the data consumer, not Avinasi itself — can run an algorithm, move data to a different location, or extract plaintext without that action being recorded on-chain. Every operation against every dataset is fully auditable.

## What this enables

**For providers**: datasets are encrypted with a per-dataset key. At rest the ciphertext sits in object storage. In memory it is decrypted only inside a TEE whose integrity can be verified by remote attestation. A consumer must hold an active on-chain rental before the **Privacy Plane** releases any decryption material.

**For consumers**: write ordinary Python against a POSIX filesystem. **Explore Mode** offers interactive Jupyter access to desensitized samples. **Secure Mode** runs batch jobs against full encrypted data inside an isolated Confidential VM. Every output passes through the **Output Gate**.

**For developers**: the architecture splits into two TEE planes. The **Privacy Plane** (Phala dstack) manages key derivation and on-chain verification. The **Computing Plane** (Google Confidential VM, Intel TDX) executes workloads in ephemeral CVMs destroyed after each job.

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
