# Foundation Models Playground

Welcome to the **DLAB Foundation Models Playground**. This web-based interface allows researchers and developers to interact with state-of-the-art biological science models without writing a single line of code.

Whether you need to predict protein structures, analyze genomic sequences, or calculate molecular properties, the Playground provides a secure, TEE-backed environment to test hypotheses and visualize results instantly.

#### Accessing the Playground

Navigate to [`https://playground.delong.lab`](https://dlab.avinasi.ai/lab/demos) and log in with your DLAB credentials.



### Usage (Upcoming...)

#### Step 1: Select a Model

Use the Model Hub to choose a model. Models are categorized by domain:

* Protein Structure: (e.g., AlphaFold variants, ESMFold)
* Genomics: (e.g., DNA/RNA language models)
* Molecular Properties: (Toxicity prediction, solubility)
* DeLong Proprietary: Exclusive internal models for specialized tasks.

> Tip: Use the AI Search bar at the top to find models using natural language (e.g., _"Find models for antibody binding affinity"_).

#### Step 2: Input Data

Once a model is selected, the Workspace will adapt to the required input format.

* Text Entry: Paste FASTA sequences or SMILES strings directly.
* File Upload: Upload `.pdb`, `.csv`, or `.fasta` files.
* Load Example: Click "Load Example" to populate the input with valid test data to see how the model works.

#### Step 3: Configure & Run

1. Adjust Parameters (e.g., recycling iterations for folding models) in the settings panel.
2. Click the Run Inference button.
3. The system will estimate the runtime (e.g., _"\~45 seconds"_).

> Security Note: Your data is processed inside a Trusted Execution Environment (TEE). No one, including platform administrators, can view your raw input or output data.
