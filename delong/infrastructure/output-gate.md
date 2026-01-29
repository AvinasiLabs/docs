# Output Gate

_Author: Dylan, Avinasi Labs_

The **Output Gate** prevents raw data from leaving the Secure Mode CVM. It combines network isolation, automated detection, and human review into a defense-in-depth stack. The goal is not perfect detection — Rice's Theorem makes that impossible for arbitrary programs — but raising the cost of data exfiltration beyond any practical benefit.

## Network isolation

Secure Mode CVMs have no outbound network access. This is enforced at the infrastructure level, not by firewall rules inside the VM. Blocking egress eliminates real-time exfiltration channels. The only way data leaves the CVM is through the Output Gate.

## Automated detection

When a job finishes, the Output Gate inspects its output before releasing it to the consumer. Detection strategies include:

| Strategy | What it catches |
|----------|----------------|
| Exact match | Output contains verbatim raw records |
| Similarity detection | Output is statistically close to input data |
| Anomaly detection | Output size or structure is suspicious |
| Differential privacy verification | Output does not satisfy a declared ε-DP bound |
| LLM semantic judgment | Output appears to encode or reconstruct raw data |

No single strategy is complete. The system combines them and produces a composite risk score.

## Risk scoring and release

If the risk score falls below the threshold, the output is released to the consumer immediately. If it exceeds the threshold, the output is held and a human review is triggered. The consumer cannot access held output until review completes.

## Human review

Reviewers can retrieve the algorithm source from the DA layer (see [Algorithm auditability](algorithm-auditability.md)), rebuild the container image to confirm its digest, and inspect the algorithm's logic. Review outcomes:

- **Approved**: output released to the consumer.
- **Rejected**: output discarded, algorithm flagged as malicious.

## Threat model

The attacker is a data consumer who submits arbitrary code. The code runs inside a TEE, so the platform cannot observe execution. Three attack paths exist:

| Attack path | Defense |
|-------------|---------|
| Output raw data directly | Automated detection |
| Exfiltrate over network | Network isolation (blocked) |
| Steganographic encoding | Extremely low bandwidth, detection heuristics |

Steganography is the hardest case. Encoding raw records into model weights or images is technically possible but requires transmitting far more output than input, which anomaly detection can flag. The economics of renting compute time to slowly exfiltrate data one record at a time work against the attacker.
