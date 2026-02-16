# Compute modes

_Author: Dylan, Avinasi Labs_

cce4long offers two execution modes. **Explore Mode** is for interactive development against desensitized samples. **Privacy Mode** is for batch computation against full encrypted datasets. Consumers typically prototype in Explore, then submit production jobs in Privacy Mode.

## Explore Mode

Explore Mode provides a JupyterLab environment running in an ordinary Kubernetes pod. The consumer gets a web IDE, a pre-installed SDK, and access to desensitized or sampled data. Because the data carries no confidentiality requirement, no TEE is needed.

Explore sessions start and stop on demand. An idle timeout reclaims resources. Outbound network access is allowed so consumers can install packages and pull documentation.

## Privacy Mode

Privacy Mode runs a batch job inside a dedicated Confidential VM (Intel TDX). The consumer submits a job specification that references a container image, datasets, and runtime parameters. `cced` verifies authorization, issues a **Job Credential**, and provisions a fresh CVM.

Inside the CVM, `tee-agent` orchestrates the workload:

1. Generates a TDX attestation report and requests keys from `cced`.
2. DecryptFS mounts the two-layer FUSE stack (JuiceFS + Decrypt FUSE) that provides plaintext only in TEE memory.
3. Executor runs the algorithm container.
4. After the job completes, `tee-agent` submits output to `cced`'s Output Gate.

The CVM has no outbound network. The consumer cannot retrieve output until the Output Gate clears it. The CVM is then destroyed.

### Container isolation

The consumer's algorithm container runs untrusted code and must be isolated from `tee-agent` and other platform processes inside the CVM.

**Mandatory controls**: no privileged mode or dangerous capabilities, read-only root filesystem, no host namespace sharing (PID/IPC/NET), `no_new_privileges` flag, seccomp and MAC (AppArmor/SELinux) profiles applied, and no access to container runtime sockets.

**Recommended controls**: non-root user inside the container, separate cgroup slice for platform processes vs. workload (preventing resource starvation of `tee-agent`), and CVM destruction after job completion to clear all runtime state.

These controls ensure that even if the algorithm container is compromised or deliberately malicious, it cannot interfere with `tee-agent`, tamper with the attestation process, or access key material held in `tee-agent`'s memory.

## Comparison

| Dimension | Explore Mode | Privacy Mode |
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
3. When the code is ready, submit a Privacy Mode job referencing the same datasets.
4. `cced` verifies authorization, issues a Job Credential, and provisions a CVM. `tee-agent` fetches encrypted data, runs the code, and submits output through the Output Gate.
5. If the Output Gate clears the result, the consumer downloads it. If the risk score exceeds the threshold, the result is held for human review.
