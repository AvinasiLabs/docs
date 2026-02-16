# cce4long

_Author: Dylan, Avinasi Labs_

**Confidential Computing Engine for Longevity** — a confidential computing engine that enables privacy-preserving AI research on sensitive longevity data. Data providers monetize datasets while keeping raw records private. Consumers run arbitrary code against that data and receive verified results. Data never leaves the TEE — only verified, auditable results are delivered.

## Trust model

Data providers upload plaintext to `cced`, which runs inside a TEE. `cced` encrypts data in TEE memory using a per-dataset key and stores only ciphertext to object storage. Plaintext exists only in TEE memory during encryption — it never reaches disk or leaves the TEE boundary unencrypted.

`cced` exposes HTTP APIs that Providers and Consumers call directly. Internally, `cced` queries the chain to verify rental status before releasing any key material. No one — not the data consumer, not Avinasi itself — can run an algorithm, move data to a different location, or extract plaintext without that action being recorded on-chain. Every operation against every dataset is fully auditable.

## What this enables

**For providers**: datasets are encrypted with a per-dataset key. At rest the ciphertext sits in object storage. In memory it is decrypted only inside a TEE whose integrity can be verified by remote attestation. A consumer must hold an active on-chain rental before `cced` releases any decryption material.

**For consumers**: write ordinary Python against a POSIX filesystem. **Explore Mode** offers interactive Jupyter access to desensitized samples. **Privacy Mode** runs batch jobs against full encrypted data inside an isolated Confidential VM. Every output passes through the **Output Gate**.

**For developers**: the architecture splits into two TEE planes. `cced` (the Privacy Plane daemon) manages key derivation, data encryption, and on-chain verification. `tee-agent` (the Computing Plane process) runs inside ephemeral CVMs that are destroyed after each job.

## Architecture at a glance

The [architecture](architecture.md) page explains how these components connect end-to-end. The pages that follow cover each layer in detail:

- [Compute modes](compute-modes.md) — Explore Mode and Privacy Mode
- [Data encryption](data-encryption.md) — per-dataset DEK, encrypted file format, two-layer FUSE stack
- [Key distribution](key-distribution.md) — wallet auth, Job Credential, Remote Attestation, ECDHE
- [Output Gate](output-gate.md) — network isolation, automated detection, review state machine, result delivery
- [Algorithm auditability](algorithm-auditability.md) — DA layer, image digest, reproducible builds
- [TEE foundations](tee-foundations.md) — confidential computing, TDX, SEV-SNP, attestation
