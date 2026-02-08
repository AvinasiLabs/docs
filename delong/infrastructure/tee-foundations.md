# TEE foundations

_Author: Dylan, Avinasi Labs_

Avinasi's security guarantees rest on hardware-enforced Trusted Execution Environments. This page covers what confidential computing provides, which hardware technologies Avinasi uses, how attestation works, and what falls outside the threat model.

## Confidential computing

A TEE isolates code and data from the host operating system, the hypervisor, and anyone with physical access to the machine. The CPU encrypts memory pages belonging to the TEE — reading them from outside returns ciphertext. The CPU also measures the code loaded into the TEE and can produce a signed report (attestation) that a remote party can verify.

This means a cloud provider that runs Avinasi CVMs cannot inspect the data being processed, even with root access to the host.

## Hardware technologies

Avinasi uses two TEE technologies:

| Technology | Plane | Hardware | Scope |
|-----------|-------|----------|-------|
| Intel TDX | Computing Plane | Google C3 Confidential VMs | Full VM isolation |
| AMD SEV-SNP | Computing Plane (alternative) | Available on multiple clouds | Full VM isolation |

The **Privacy Plane** runs on Phala dstack, which provides multi-node high-availability TEE deployment with its own KMS, attestation agent, and RA-TLS support.

Intel TDX (Trust Domain Extensions) creates isolated virtual machines called Trust Domains. The CPU encrypts all TD memory and prevents the VMM from reading or modifying it. SEV-SNP (Secure Encrypted Virtualization — Secure Nested Paging) from AMD provides equivalent guarantees with a different hardware implementation.

## Attestation

Attestation lets a remote party verify that a CVM is running expected code on genuine TEE hardware. The process works in three steps:

1. The CVM asks the CPU to produce a hardware-signed report containing measurements of the loaded code and a caller-supplied data field (used to bind an ECDHE public key).
2. The report is sent to the verifier (the Privacy Plane or an external attestation service).
3. The verifier checks the CPU signature, confirms the measurements match a known-good value, and validates the caller-supplied data.

Avinasi uses Trustee (from the CNCF confidential-containers project) for attestation verification. Trustee supports Intel TDX, AMD SEV-SNP, Intel SGX, Arm CCA, and other backends. It runs as a Kubernetes operator and has production deployments on OpenShift.

## Threat model

TEE protects against:

- Malicious cloud operators (host OS, hypervisor, firmware)
- Physical access attacks (cold boot, bus snooping)
- Co-tenant side channels at the VM boundary

TEE does not protect against:

- Bugs in the code running inside the TEE
- Denial of service (the host can refuse to schedule the TEE)
- CPU hardware vulnerabilities (microarchitectural side channels have been demonstrated against older TEE implementations; TDX and SEV-SNP include mitigations)

Avinasi treats TEE as the foundation layer. The [Output Gate](output-gate.md) and [key distribution](dek-distribution.md) protocols add application-level defenses on top of it.
