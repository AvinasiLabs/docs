# Algorithm auditability

_Author: Dylan, Avinasi Labs_

When the **Output Gate** holds a result for human review, the reviewer needs to understand what the algorithm does. Auditability requires that every algorithm submitted to Privacy Mode has a verifiable identity and retrievable source. This page covers the identity scheme, storage layout, and reproducible build process.

## Algorithm identity

Each algorithm submission produces four references:

- **WorkloadRef**: the container image digest (`sha256:9f86d081...`), an immutable content hash.
- **Invocation**: entrypoint, arguments, and environment variables.
- **InputRef**: dataset IDs, snapshot version, and DEK reference.
- **AttestationRef**: TDX measurement and timestamp from the CVM that ran the job.

The image digest is the primary identifier. Two submissions with the same digest ran the same code, regardless of tag or registry metadata.

## Storage layout

| Content | Location | Retention |
|---------|----------|-----------|
| Source bundle (.tar.gz) | DA layer | Short-term (~7 days) |
| Lockfile / SBOM | DA layer | Short-term |
| Dockerfile | DA layer | Short-term |
| Image digest | On-chain | Permanent |
| Source hash | On-chain | Permanent |
| DA blob ID | On-chain | Permanent |

Source code and build artifacts go to a data availability layer for short-term retrieval during the review window. On-chain records are permanent and allow anyone to verify that a given source hash corresponds to a given image digest, even after the DA blob expires.

## Reproducible builds

A reviewer who downloads the source from the DA layer should be able to rebuild the container image and get the same digest. Reproducibility depends on pinned dependencies:

- **Lockfile pinning**: `poetry.lock` or `package-lock.json` fixes dependency versions.
- **Vendored dependencies**: packaging all dependencies into the source bundle eliminates network fetches during build.

Full deterministic builds via Nix or Bazel are possible but add complexity beyond what is needed at launch. The minimum requirement is that the rebuilt image produces the same digest as the on-chain record.

## Review workflow

1. Output Gate flags a result (risk score above threshold).
2. Reviewer downloads algorithm source from DA layer using the on-chain blob ID.
3. Reviewer inspects algorithm logic.
4. Reviewer rebuilds the image and confirms the digest matches the on-chain record.
5. Reviewer approves or rejects the output.

This process is an automatic trigger, not a complaint system. Consumers cannot access held output until review completes.
