# Overview

DeLong is a decentralized longevity data securitization protocol, enabling datasets to be tokenized as income-generating securities backed by rental revenue.

## What DeLong Does

DeLong transforms longevity research datasets into tradeable asset-backed securities. Research teams tokenize their datasets and raise capital through Initial Data Offerings. Researchers pay to access the data. Token holders receive proportional dividends from rental revenue and maintain governance control over project operations.

The protocol removes trust requirements through code-enforced protections. Treasury funds cannot be withdrawn without token holder approval. Trading liquidity is permanently guaranteed through locked Uniswap pools. Failed projects enable proportional capital recovery through formal delisting procedures.

## Core Components

**Initial Data Offering** – Projects raise capital through continuous price discovery where earlier participants receive proportionally better terms. When the funding goal is reached, raised funds are split between creating a permanent Uniswap liquidity pool and depositing treasury reserves under governance control.

**Governance** – Token holders vote on treasury allocations, operational parameters, and project continuation. All fund withdrawals require proposal approval through on-chain voting. No privileged roles or admin keys exist that could bypass governance.

**Rental Revenue** – Researchers pay rental fees to access datasets in privacy-preserving computation environments. The majority of rental payments are distributed to token holders as dividends, with a small portion allocated to protocol operations.

**Permanent Liquidity** – Uniswap LP tokens are sent directly to the governance contract during launch with no removal mechanism. This guarantees secondary market trading throughout the project lifecycle.

**Capital Recovery** – Token holders can vote to shut down underperforming or abandoned projects. The protocol returns remaining treasury funds plus recovered liquidity pool assets proportionally to token holders.

## Dataset Access

Datasets are stored in Trusted Execution Environments to protect sensitive medical and personal data. Researchers access datasets through secure computation without downloading raw files, enabling privacy-preserving analysis.

DeLong Lab provides the first application layer, offering researchers Jupyter notebooks in TEE environments, development tools, and longevity research models. The protocol's economic infrastructure supports building additional analysis applications and AI tools.

## Key Properties

**Protocol-enforced custody** – Smart contracts mathematically prevent treasury withdrawals without governance approval. Code enforcement replaces trust in team honesty.

**Guaranteed liquidity** – Permanent LP locking ensures token holders can always exit through secondary markets. Teams cannot remove trading liquidity under any circumstances.

**Automatic distribution** – Rental payments flow directly to token holders without manual processing or periodic batch settlements.

**Transparent operations** – All governance actions, fund movements, and protocol interactions are verifiable on-chain through public contract state and verified source code.
