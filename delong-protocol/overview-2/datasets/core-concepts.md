# Core Concepts

#### 1. TEE (Trusted Execution Environment)

A **Trusted Execution Environment** is a secure area of a processor that guarantees code and data are protected with respect to confidentiality and integrity. The library automatically detects TEE environments through attestation.

#### 2. Attestation Flow

```
┌─────────┐     ┌──────────────┐     ┌──────────────┐     ┌─────────┐
│ Client  │───▶│ TEE Attestor │───—▶│ Verification │───▶│ Backend │
│ Library │     │ (Unix Socket)│     │   Service    │     │   API   │
└─────────┘     └──────────────┘     └──────────────┘     └─────────┘
                      Token                Cipher            Decision
```

1. **Client** requests attestation token from local TEE service
2. **TEE Attestor** provides cryptographically signed token
3. **Verification Service** validates token
4. **Backend API** verifies cipher and grants access to real data

#### 3. Data Access Modes

| Mode      | Environment | Data Returned | Use Case                              |
| --------- | ----------- | ------------- | ------------------------------------- |
| **Local** | Non-TEE     | Sample data   | Development, testing, debugging       |
| **TEE**   | Secure TEE  | Real data     | Production analysis on sensitive data |

#### 4. Zero-Trust Client

The client library does **NOT** decide whether it's running in a secure environment. It only:

* Fetches attestation credentials
* Backend makes all authorization decisions

This prevents malicious clients from bypassing security controls.
