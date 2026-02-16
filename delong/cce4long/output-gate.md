# Output Gate

_Author: Dylan, Avinasi Labs_

The **Output Gate** prevents raw data from leaving the Privacy Mode CVM. It combines network isolation, automated detection, and human review into a defense-in-depth stack. The goal is not perfect detection — Rice's Theorem makes that impossible for arbitrary programs — but raising the cost of data exfiltration beyond any practical benefit.

Output Gate is one of `cced`'s internal modules. When `tee-agent` submits a result, `cced` verifies the CVM's attestation report, decrypts and inspects the output, and decides whether to release it.

## Network isolation

Privacy Mode CVMs have no outbound network access. This is enforced at the infrastructure level, not by firewall rules inside the VM. Blocking egress eliminates real-time exfiltration channels. The only way data leaves the CVM is through `tee-agent`'s SubmitResult call to `cced`.

## Result Encryption Key (REK)

Each job receives a **Result Encryption Key** (REK) alongside its DEKs during the key exchange at job startup. The REK is derived by `cced`'s Key Manager from the `job_id`, so `cced` can re-derive the same REK at any time without the CVM transmitting it back.

When the job finishes, `tee-agent` encrypts the output with the REK (AES-256-GCM) and uploads the ciphertext to object storage. `tee-agent` then submits a result notification to `cced` containing the `job_id`, result path, and result hash — but not the REK itself. `cced` re-derives the REK, downloads and decrypts the result for inspection.

This design means the Output Gate can inspect every result, and no output reaches the consumer without passing through review.

## Automated detection

When a result is submitted, the Output Gate decrypts it in TEE memory and runs detection strategies:

| Strategy | What it catches |
|----------|----------------|
| Exact match | Output contains verbatim raw records |
| Similarity detection | Output is statistically close to input data |
| Anomaly detection | Output size or structure is suspicious |
| Differential privacy verification | Output does not satisfy a declared DP bound |
| LLM semantic judgment | Output appears to encode or reconstruct raw data |

No single strategy is complete. The system combines them and produces a composite risk score (0–1).

## Review state machine

```
pending_review → auto_approved     (risk score below threshold)
pending_review → needs_human       (risk score at or above threshold)
needs_human    → approved          (human or DAO approval)
needs_human    → rejected          (human or DAO rejection)
```

If the risk score falls below the threshold, the output transitions to `auto_approved` and is released immediately. If the score meets or exceeds the threshold, the output enters `needs_human` and the consumer cannot access it until review completes.

## Human and DAO review

When a result enters `needs_human`, the data provider (dataset owner) is notified. The provider has two options:

- **Self-review**: inspect the algorithm source from the DA layer (see [Algorithm auditability](algorithm-auditability.md)), optionally rebuild the container image to confirm its digest, and approve or reject.
- **DAO review**: submit the case to a DAO vote. Token holders for that dataset vote to approve or reject. This path suits providers who prefer community governance over individual judgment.

Review outcomes:

- **Approved**: output released to the consumer.
- **Rejected**: output discarded, algorithm flagged as malicious.

## Result delivery

After a result reaches `approved` or `auto_approved` status, the consumer can request it:

1. Consumer calls `cced` with their `job_id` and a wallet signature.
2. `cced` verifies the consumer's identity and confirms the result's review status is approved.
3. `cced` returns a signed **Result Manifest** (containing `job_id`, result path, result hash, and review decision) along with the REK encrypted to the consumer.
4. Consumer downloads the encrypted result from object storage.
5. Consumer decrypts the REK, verifies the Result Manifest signature and result hash, and decrypts the result.

The Result Manifest serves as a provenance certificate — the consumer can present it to third parties as proof that the result passed Output Gate review.

## Threat model

The attacker is a data consumer who submits arbitrary code. The code runs inside a TEE, so the platform cannot observe execution. Three attack paths exist:

| Attack path | Defense |
|-------------|---------|
| Output raw data directly | Automated detection |
| Exfiltrate over network | Network isolation (blocked) |
| Steganographic encoding | Extremely low bandwidth, detection heuristics |

Steganography is the hardest case. Encoding raw records into model weights or images is technically possible but requires transmitting far more output than input, which anomaly detection can flag. The economics of renting compute time to slowly exfiltrate data one record at a time work against the attacker.
