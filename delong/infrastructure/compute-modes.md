# Compute modes

_Author: Dylan, Avinasi Labs_

Avinasi offers two execution modes. **Explore Mode** is for interactive development against desensitized samples. **Secure Mode** is for batch computation against full encrypted datasets. Consumers typically prototype in Explore, then submit production jobs in Secure.

## Explore Mode

Explore Mode provides a JupyterLab environment running in an ordinary Kubernetes pod. The consumer gets a web IDE, a pre-installed SDK, and access to desensitized or sampled data. Because the data carries no confidentiality requirement, no TEE is needed.

Explore sessions start and stop on demand. An idle timeout reclaims resources. Outbound network access is allowed so consumers can install packages and pull documentation.

## Secure Mode

Secure Mode runs a batch job inside a dedicated Google Confidential VM (Intel TDX, C3 series). The consumer submits a job specification (YAML or JSON) that references a container image, datasets, and runtime parameters. The platform provisions a fresh CVM, injects an **Authorization Token**, and executes the workload.

The CVM has no outbound network. Data is accessed through the two-layer FUSE stack (JuiceFS + Decrypt FUSE) that provides plaintext only in TEE memory. After the job completes, all output passes through the **Output Gate** before the consumer can retrieve it. The CVM is then destroyed.

## Comparison

| Dimension | Explore Mode | Secure Mode |
|-----------|-------------|-------------|
| Interface | JupyterLab (web IDE) | Job spec (YAML/JSON) |
| Execution | Real-time interactive | Async batch |
| Data | Desensitized samples | Full encrypted datasets |
| Environment | Kubernetes pod | Independent CVM (TDX) |
| Network | Egress allowed | No egress |
| Output check | None | Output Gate enforced |
| Lifecycle | User-controlled, idle timeout | Destroyed after completion |

## Workflow

A typical consumer session looks like this:

1. Rent dataset access on-chain.
2. Open Explore Mode. Load desensitized samples, iterate on analysis code.
3. When the code is ready, submit a Secure Mode job referencing the same datasets.
4. The platform provisions a CVM, fetches encrypted data, runs the code, and passes output through the Output Gate.
5. If the Output Gate clears the result, the consumer downloads it. If the risk score exceeds the threshold, the result is held for human review.
