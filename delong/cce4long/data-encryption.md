# Data encryption

_Author: Dylan, Avinasi Labs_

Every dataset is encrypted with its own key before it reaches object storage. Plaintext exists only inside TEE memory during computation. This page covers the key hierarchy, the two-layer FUSE stack that makes decryption transparent, and the key lifecycle.

## Per-dataset DEK

Each dataset gets an independent **Data Encryption Key** (DEK) derived from `cced`'s hardware-bound root key using the dataset identifier as input:

`DEK = DStack.getKey(dataset_id, "dataset-encryption")`

Encryption uses AES-256-GCM. The per-dataset granularity means revoking or delisting one dataset does not affect any other. A consumer who rents three datasets receives three separate DEKs, each scoped to a single dataset.

Data encryption is handled as internal business logic within `cced` — the daemon receives plaintext from the provider, encrypts it using a DEK from its Key Manager module, and stores ciphertext to object storage. The provider never holds the DEK.

## Two-layer FUSE stack

Privacy Mode CVMs mount two FUSE filesystems in sequence. Consumer code reads from a standard POSIX path and is unaware of either layer.

**JuiceFS** mounts the encrypted dataset from S3. It handles chunking, caching, and metadata. Everything it writes to local cache is still ciphertext, so disk exposure is safe.

**Decrypt FUSE** sits above JuiceFS and intercepts every read call. It fetches ciphertext from JuiceFS, decrypts it in memory using the dataset's DEK, and returns plaintext to the caller. Plaintext never touches disk.

For a consumer reading `/data/42/file.parquet`, the call chain is:

1. Decrypt FUSE intercepts the read at `/data/42/`.
2. Decrypt FUSE requests ciphertext from JuiceFS at `/mnt/jfs/42/`.
3. JuiceFS fetches encrypted chunks from S3.
4. Decrypt FUSE decrypts in memory using DEK-42.
5. Plaintext returns to consumer code.

When a job accesses multiple datasets, each dataset gets its own Decrypt FUSE instance holding its own DEK. This is managed by `tee-agent`'s DecryptFS module.

## Encrypted file format

Files are stored in a chunked encryption format with a simple header:

- **Header**: a 4-byte magic number (`AVIN`), a 1-byte version identifier, and a chunk count.
- **Chunks**: each chunk contains a 96-bit random IV, ciphertext (AES-256-GCM), and a 128-bit authentication tag. Each chunk is encrypted independently with its own IV.

The default chunk size is 4 MB. Chunking serves four purposes: streaming uploads (data can be encrypted and transmitted incrementally), resumable transfers (a failed upload resumes from the last completed chunk), parallel encryption and decryption (chunks are independent), and range reads (the Computing Plane can decrypt a specific chunk without processing the entire file).

## Why not JuiceFS native encryption

JuiceFS supports client-side encryption, but it only accepts one key per mount. cce4long needs per-dataset keys so that access can be granted and revoked independently. Using a separate Decrypt FUSE layer also keeps key management inside `cced`'s domain rather than delegating it to JuiceFS internals.

## Key lifecycle

DEKs are derived deterministically from the root key. There is no separate key rotation — rotating means re-encrypting the dataset with a new derived key. Delisting a dataset on-chain causes `cced` to stop issuing that dataset's DEK. Existing ciphertext in S3 becomes inert.
