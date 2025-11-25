# what is DeLong Protocol

### 1. Overview 🌟 <a href="#id-1-overview" id="id-1-overview"></a>

DeLong is a trustless, privacy-preserving computation protocol tailored for longevity and biomedical research. It combines blockchain-based governance with secure TEE (Trusted Execution Environment) computation to enable researchers to analyze sensitive bio-data without compromising privacy. By leveraging decentralized governance and hardware-enforced confidentiality, DeLong allows scientists to perform complex analyses on encrypted datasets contributed by users, all while ensuring data providers retain full privacy and control. This DeLong Protocol is currently deployed on the Phala Network, integrating its large-scale TEE infrastructure with on-chain DAO governance for algorithm approval.

> **GitHub Repository**: [https://github.com/AvinasiLabs/delong](https://github.com/AvinasiLabs/delong)

### 2. System Architecture 🏗️ <a href="#id-2-system-architecture" id="id-2-system-architecture"></a>

#### 2.1 Core Components <a href="#id-2-1-core-components" id="id-2-1-core-components"></a>

* **Blockchain Smart Contracts** 🔄: Handle governance and logging
* **TEE Secure Computing Nodes** 🔒: Execute privacy-preserving computations
* **Decentralized Storage** 💾: Securely store encrypted data
* **DAO Governance System** 🏛️: Community-driven algorithm approval mechanism

#### 2.2 Technology Stack <a href="#id-2-2-technology-stack" id="id-2-2-technology-stack"></a>

* Blockchain Technology: For governance and verification
* TEE Technology: For secure computation
* Encryption Technology: For data protection
* Phala Network: Provides underlying TEE infrastructure

### 3. System Workflow ⚙️ <a href="#id-3-system-workflow" id="id-3-system-workflow"></a>

#### 3.1 Data Contribution Process <a href="#id-3-1-data-contribution-process" id="id-3-1-data-contribution-process"></a>

1. **Data Encryption** 🔐
   * Users encrypt sensitive biodata using the TEE node's public key
   * Encrypted data packages are submitted to the DeLong Protocol
   * Data is stored in TEE secure storage or distributed storage
2. **Data Access Control** 🛡️
   * Data owners can customize access policies
   * Encrypted data can only be decrypted within authorized TEEs
   * Ownership and access are recorded on the blockchain

#### 3.2 Algorithm Execution <a href="#id-3-2-algorithm-execution" id="id-3-2-algorithm-execution"></a>

1. **Algorithm Submission and Approval** 📋
   * Researchers submit algorithm code
   * Community DAO reviews and approves
   * Algorithm versions and changes are recorded on-chain
2. **Secure Computation Process** 💻
   * Data and algorithms are decrypted inside TEE
   * Computation is executed securely
   * Only results are returned to authorized recipients

### 4. Key Features 🚀 <a href="#id-4-key-features" id="id-4-key-features"></a>

#### 4.1 Privacy Guarantees <a href="#id-4-1-privacy-guarantees" id="id-4-1-privacy-guarantees"></a>

* **End-to-End Encryption** 🔒: Data remains encrypted from source to processing
* **Zero-Knowledge Proofs** 🛡️: Verify computation results without accessing raw data
* **Privacy Auditing** 📝: All data access is recorded on the blockchain

#### 4.2 Trustless Architecture <a href="#id-4-2-trustless-architecture" id="id-4-2-trustless-architecture"></a>

* **Hardware Trusted Execution Environment** 💪: Leverages modern CPU security features
* **Remote Attestation** ✅: Verifies authenticity and integrity of computing nodes
* **Open-Source Code Verification** 👁️: All components are open-source for auditing

#### 4.3 Application Scenarios <a href="#id-4-3-application-scenarios" id="id-4-3-application-scenarios"></a>

* Genomic data analysis
* Clinical trial data sharing
* Health record analytics
* Drug discovery research
* Personalized medicine solutions

### 5. Technical Implementation Details ⚡ <a href="#id-5-technical-implementation-details" id="id-5-technical-implementation-details"></a>

#### 5.1 TEE Implementation <a href="#id-5-1-tee-implementation" id="id-5-1-tee-implementation"></a>

1. **Using Intel SGX** 🖥️
   * Hardware-based memory encryption
   * Code integrity verification
   * Data isolation protection
2. **Storage and Computation Separation** 📊
   * Encrypted data stored in distributed systems
   * Computation performed only in verified TEEs
   * Results re-encrypted for transmission

#### 5.2 Blockchain Integration <a href="#id-5-2-blockchain-integration" id="id-5-2-blockchain-integration"></a>

1. **Phala Network Integration** 🔗
   * Utilizes Phala's TEE worker network
   * On-chain TEE attestation mechanisms
   * Secure messaging system
2. **Smart Contract Governance** 📜
   * Automated algorithm approval process
   * Data access permission management
   * Computation request validation and authorization

### 6. User Value 💎 <a href="#id-6-user-value" id="id-6-user-value"></a>

#### 6.1 Value for Researchers <a href="#id-6-1-value-for-researchers" id="id-6-1-value-for-researchers"></a>

* Access to larger-scale datasets
* No need to handle complex data privacy issues
* Accelerated research progress and breakthroughs
* Reduced research compliance costs

#### 6.2 Value for Data Contributors <a href="#id-6-2-value-for-data-contributors" id="id-6-2-value-for-data-contributors"></a>

* Complete control over personal data permissions
* Support scientific progress while protecting privacy
* Track data usage
* Participate in governance decisions

### 7. Open Source Commitment 🌐 <a href="#id-7-open-source-commitment" id="id-7-open-source-commitment"></a>

All components of the DeLong Protocol—from smart contracts to enclave code and client libraries—are released as open-source software. This open-source commitment serves several vital purposes:

* **Transparency and Trust**: Anyone can inspect how DeLong works
* **Reproducibility and Scientific Rigor**: Others can reproduce and build upon our work
* **Community-Driven Development**: Contributors from around the world can help enhance the DeLong Protocol
* **Ethical Alignment**: As the DeLong Protocol handles sensitive personal data, integrity and openness are essential

The complete source code is available in our [GitHub repository](https://github.com/AvinasiLabs/delong).

### 8. Vision and Future 🔮 <a href="#id-8-vision-and-future" id="id-8-vision-and-future"></a>

DeLong aims to become a cornerstone of privacy-preserving scientific computing that accelerates discoveries in longevity and broader biomedical research. We envision a future where sensitive medical and biological data from around the world—genomic sequences, clinical trial data, health records—can be pooled virtually without compromising individual privacy.

Future development directions include:

* Expanding support for more biomedical research fields
* Integrating federated learning technologies
* Exploring diverse TEE hardware support
* Enhancing community governance mechanisms

### 9. Join Us 🤝 <a href="#id-9-join-us" id="id-9-join-us"></a>

We invite scientists, developers, and forward-thinking institutions to join our journey:

* **For Researchers**: Consider leveraging DeLong for your studies
* **For Data Contributors/Patient Advocates**: Learn how your data can safely fuel discoveries
* **For Developers/Security Experts**: Contribute to the codebase or perform audits

By working together, we can build the DeLong Protocol that not only advances longevity science but also sets a new standard for ethical, secure, and decentralized computation.

**Get Involved**: Explore our [GitHub repository](https://github.com/AvinasiLabs/delong) to learn more about the project and find ways to contribute.
