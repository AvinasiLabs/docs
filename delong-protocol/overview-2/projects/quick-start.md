# Quick Start

Navigate to [DLAB Projects](https://dlab.avinasi.ai/login?returnUrl=%2Flab%2Fprojects) and login.

Follow these steps to launch your first secure research environment:

1. Create a Project:
   * Navigate to the Dashboard and click + New Project.
   * Enter a Project Name (e.g., _"Alpha-Kinase Inhibitor Study"_).
   * Select a Visibility Level: _Private_ (encrypted, invite-only) or _Public_ (open science).
2. Select Environment (Upcoming):
   * Choose a base image. We provide pre-configured environments for:
     * Standard Bio: Python, Biopython, Pandas, NumPy.
     * Deep Learning: PyTorch/TensorFlow with CUDA support.
     * DeLong Native: Pre-installed with `delong-datasets` and `delong-models` SDKs.
3. Compute Resources (Upcoming):
   * Select your TEE Instance type (e.g., _TEE-GPU-A100_ for heavy training or _TEE-CPU-Std_ for data cleaning).
4. Launch:
   * Click Start Workspace. Your secure JupyterLab environment will spin up in approximately 1-5 minutes.
