# Key Features

#### Automatic TEE Detection

The library automatically detects whether it's running in a secure TEE environment:

* **Development/Local**: Returns sample data for testing
* **Secure TEE**: Returns real sensitive data after attestation

No configuration needed - it just works with automatic environment awareness!

#### Column Filtering

Request only the columns you need:

```python
from delong_datasets import download_dataset, DownloadOptions

opts = DownloadOptions(columns=["patient_id", "diagnosis"])
data = download_dataset("<dataset_id>", token, opts)
```

#### Streaming Large Datasets

Process datasets that don't fit in memory:

```python
opts = DownloadOptions(stream=True)
dataset = download_dataset("<dataset_id>", token, opts)

for batch in dataset.iter(batch_size=1000):
    process(batch)
```

#### Multiple Export Formats

```python
from delong_datasets import export_data

# Export to CSV, JSON, or Parquet
export_data(data, format="csv", path="/tmp/output.csv")
export_data(data, format="parquet", path="/tmp/output.parquet")
```
