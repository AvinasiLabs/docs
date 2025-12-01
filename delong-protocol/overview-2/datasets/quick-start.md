# Quick Start

#### Installation

```bash
# Clone repository
git clone git@github.com:AvinasiLabs/delong-datasets.git
cd delong-datasets

# Install in development mode
pip install -e .
```

#### Basic Usage

```python
from delong_datasets import download_dataset

# Download dataset (automatic TEE detection)
data = download_dataset("<dataset_id>", token="<your-token>")

print(f"Loaded {data.num_rows} rows")
print(data.column_names)

# Convert to pandas
import pandas as pd
df = pd.DataFrame(data)
print(df.head())
```

#### Command Line

```bash
# Set environment
export TOKEN="your-access-token"

# Download dataset
python -m delong_datasets download dataset-id --token $TOKEN
```

***

###
