# TEE foundations

_Author: Dylan, Avinasi Labs_

cce4long's security guarantees rest on hardware-enforced Trusted Execution Environments. This page covers what confidential computing provides, which hardware technologies are used, how attestation works, and what falls outside the threat model.

## Confidential computing

A TEE isolates code and data from the host operating system, the hypervisor, and anyone with physical access to the machine. The CPU encrypts memory pages belonging to the TEE — reading them from outside returns ciphertext. The CPU also measures the code loaded into the TEE and can produce a signed report (attestation) that a remote party can verify.

This means a cloud provider that runs cce4long CVMs cannot inspect the data being processed, even with root access to the host.

## Hardware technologies

cce4long uses Confidential VM technologies for workload isolation:

| Technology | Use | Hardware | Scope |
|-----------|------|----------|-------|
| Intel TDX | Computing Plane (Privacy Mode CVMs) | Google C3 series (or equivalent) | Full VM isolation |
| AMD SEV-SNP | Computing Plane (alternative) | Available on multiple clouds | Full VM isolation |

`cced` (the Privacy Plane daemon) also runs inside a TEE to protect key material and policy enforcement logic.

Intel TDX (Trust Domain Extensions) creates isolated virtual machines called Trust Domains. The CPU encrypts all TD memory and prevents the VMM from reading or modifying it. SEV-SNP (Secure Encrypted Virtualization — Secure Nested Paging) from AMD provides equivalent guarantees with a different hardware implementation.

## Attestation

Attestation lets a remote party verify that a CVM is running expected code on genuine TEE hardware. The process works in three steps:

1. `tee-agent` asks the CPU to produce a hardware-signed report containing measurements of the loaded code and a caller-supplied data field (used to bind an ECDHE public key).
2. The report is sent to `cced`, which acts as the verifier via its TEE Verifier module.
3. `cced` checks the CPU signature, confirms the measurements match a known-good value registered in the measurement whitelist, and validates the caller-supplied data.

## Threat model

TEE protects against:

- Malicious cloud operators (host OS, hypervisor, firmware)
- Physical access attacks (cold boot, bus snooping)
- Co-tenant side channels at the VM boundary

TEE does not protect against:

- Bugs in the code running inside the TEE
- Denial of service (the host can refuse to schedule the TEE)
- CPU hardware vulnerabilities (microarchitectural side channels have been demonstrated against older TEE implementations; TDX and SEV-SNP include mitigations)

cce4long treats TEE as the foundation layer. The [Output Gate](output-gate.md) and [key distribution](key-distribution.md) protocols add application-level defenses on top of it.
