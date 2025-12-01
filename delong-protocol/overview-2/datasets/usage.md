# Usage

### Basic Usage

#### Downloading Datasets

**Simple Download**

```python
from delong_datasets import download_dataset

# Minimal usage
data = download_dataset("<dataset_id>", "your-token")
print(data)
```

**With Options**

```python
from delong_datasets import download_dataset, DownloadOptions

# Configure download options
opts = DownloadOptions(
    columns=["patient_id", "diagnosis", "age"],  # Column filtering
    limit=1000,                                    # Row limit
    stream=False                                   # Streaming mode
)

data = download_dataset("<dataset_id>", "your-token", opts)
```

**Streaming Large Datasets**

```python
from delong_datasets import download_dataset, DownloadOptions

# Stream dataset (memory efficient)
opts = DownloadOptions(stream=True)
dataset = download_dataset("<dataset_id>", "your-token", opts)

# Iterate over batches
for batch in dataset.iter(batch_size=100):
    process_batch(batch)
```

#### Working with Data

**Convert to Pandas**

```python
import pandas as pd
from delong_datasets import download_dataset

data = download_dataset("<dataset_id>", "your-token")
df = pd.DataFrame(data)

# Standard pandas operations
print(df.describe())
print(df.groupby('diagnosis').size())
```

**Convert to PyArrow**

```python
data = download_dataset("<dataset_id>", "your-token")
table = data.to_pandas()  # Returns pyarrow.Table
```

**Access as NumPy**

```python
data = download_dataset("<dataset_id>", "your-token")

# Get specific column as numpy array
ages = data['age'].to_numpy()
print(f"Mean age: {ages.mean()}")
```

#### Exporting Data

**Export to CSV**

```python
from delong_datasets import download_dataset, export_data

# Download data
data = download_dataset("<dataset_id>", "your-token")

# Export to CSV
export_data(data, format="csv", path="/tmp/output.csv")
```

**Export to Parquet**

```python
export_data(data, format="parquet", path="/tmp/output.parquet")
```

**Export to JSON**

```python
export_data(data, format="json", path="/tmp/output.json")
```

**CLI Export**

```bash
python -m delong_datasets export <dataset_id> \
    --token $TOKEN \
    --format csv \
    --output /tmp/data.csv \
    --limit 5000
```

***

### Advanced Features

#### Column Filtering

Request only the columns you need to reduce bandwidth and improve performance:

```python
from delong_datasets import download_dataset, DownloadOptions

# Only download specific columns
opts = DownloadOptions(columns=["patient_id", "diagnosis"])
data = download_dataset("<dataset_id>", "your-token", opts)

print(data.column_names)  # ['patient_id', 'diagnosis']
```

**Benefits:**

* Reduced network bandwidth
* Faster downloads
* Lower memory usage
* Privacy: don't access columns you don't need

#### Pagination

Handle large datasets efficiently with pagination:

```python
from delong_datasets import download_dataset, DownloadOptions

# Download in pages
page_size = 1000
offset = 0

while True:
    opts = DownloadOptions(limit=page_size, offset=offset)
    data = download_dataset("<dataset_id>", "your-token", opts)

    if data.num_rows == 0:
        break

    process_page(data)
    offset += page_size
```

#### Streaming Mode

For very large datasets that don't fit in memory:

```python
from delong_datasets import download_dataset, DownloadOptions

opts = DownloadOptions(stream=True)
dataset = download_dataset("<dataset_id>", "your-token", opts)

# Process in batches
for batch in dataset.iter(batch_size=1000):
    # Each batch is a dict of column_name -> list of values
    patient_ids = batch['patient_id']
    diagnoses = batch['diagnosis']

    # Process this batch
    results = analyze_batch(patient_ids, diagnoses)
    save_results(results)
```

#### Custom Timeout and Retries

```python
from delong_datasets import download_dataset, DownloadOptions

opts = DownloadOptions(
    timeout_sec=60,      # Wait up to 60 seconds
    max_retries=5        # Retry up to 5 times on failure
)

data = download_dataset("<dataset_id>", "your-token", opts)
```

#### Working with Multiple Datasets

```python
from delong_datasets import download_dataset

datasets = {}
dataset_ids = ["<dataset_id_0>", "<dataset_id_1>"]

for dataset_id in dataset_ids:
    datasets[dataset_id] = download_dataset(dataset_id, "your-token")
    print(f"Loaded {dataset_id}: {datasets[dataset_id].num_rows} rows")

# Combine datasets
import pandas as pd
combined_df = pd.concat([
    pd.DataFrame(datasets["<dataset_id_0>"]),
    pd.DataFrame(datasets["<dataset_id_1>"])
], ignore_index=True)
```
