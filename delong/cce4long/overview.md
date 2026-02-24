# cce4long

_Author: Dylan, Avinasi Labs_

**Confidential Computing Engine for Longevity** — a confidential computing engine for AI research on sensitive longevity data.

## What cce4long delivers

cce4long provides three guarantees:

1. **Privacy** — Data stays encrypted inside hardware-secured enclaves. Plaintext exists only in TEE memory during computation and never leaves the trust boundary. Data providers monetize their data without losing control.
2. **Portability** — cce4long exposes data through a standard POSIX filesystem (JuiceFS + FUSE). Existing Python, R, PyTorch, and scientific computing toolchains work without modification. No proprietary SDK required.
3. **Verifiability** — Every result is signed and auditable on-chain. TEE attestation proves execution identity. The Output Gate reviews all outputs before release, and signed Result Manifests provide tamper-evident provenance. No black-box answers.

cce4long does not deliver compute time — it delivers **verifiable results**.

## Trust model

Data providers upload plaintext to the Privacy Plane, which runs inside a TEE. The Privacy Plane encrypts data in TEE memory using a per-dataset key and stores only ciphertext to object storage. Plaintext exists only in TEE memory during encryption — it never reaches disk or leaves the TEE boundary unencrypted.

The Privacy Plane exposes HTTP APIs that Providers and Consumers call directly. Internally, it queries the chain to verify rental status before releasing any key material. No one — not the data consumer, not Avinasi itself — can run an algorithm, move data to a different location, or extract plaintext without that action being recorded on-chain. Every operation against every dataset is fully auditable.

## What this enables

**For providers**: datasets are encrypted with a per-dataset key. At rest the ciphertext sits in object storage. In memory it is decrypted only inside a TEE whose integrity can be verified by remote attestation. A consumer must hold an active on-chain rental before the Privacy Plane releases any decryption material.

**For consumers**: write ordinary Python against a POSIX filesystem. **Explore Mode** offers interactive Jupyter access to desensitized samples. **Privacy Mode** runs batch jobs against full encrypted data inside an isolated Confidential VM. Every output passes through the **Output Gate**.

**For developers**: the architecture splits into two TEE planes. The Privacy Plane manages key derivation, data encryption, and on-chain verification. The Computing Plane runs consumer workloads inside ephemeral CVMs that are destroyed after each job. See [Architecture](architecture.md) for implementation details.

## Architecture at a glance

The [architecture](architecture.md) page explains how these components connect end-to-end. The pages that follow cover each layer in detail:

- [Compute modes](compute-modes.md) — Explore Mode and Privacy Mode
- [Data encryption](data-encryption.md) — per-dataset DEK, encrypted file format, two-layer FUSE stack
- [Key distribution](key-distribution.md) — wallet auth, Job Credential, Remote Attestation, ECDHE
- [Output Gate](output-gate.md) — network isolation, automated detection, review state machine, result delivery
- [Algorithm auditability](algorithm-auditability.md) — DA layer, image digest, reproducible builds
- [TEE foundations](tee-foundations.md) — confidential computing, TDX, SEV-SNP, attestation
