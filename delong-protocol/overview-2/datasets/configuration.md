# Configuration

#### Environment Variables

Configure the library using environment variables:

| Variable                   | Description                    |
| -------------------------- | ------------------------------ |
| `DS_ATTESTATION_ENDPOINT`  | Remote attestation service URL |
| `DS_ATTESTATION_SOCKET`    | Local TEE attestor socket path |
| `DS_ATTESTATION_AUDIENCE`  | Attestation token audience     |
| `DS_ATTESTATION_TIMEOUT`   | Attestation timeout (seconds)  |
| `DS_TIMEOUT`               | API request timeout (seconds)  |
| `DS_MAX_RETRIES`           | Maximum retry attempts         |
| `DS_DEFAULT_LIMIT`         | Default row limit              |
| `DS_MAX_LOCAL_EXPORT_ROWS` | Maximum rows for local export  |

#### Configuration File

Create a `.env` file in your project:

```bash
# Performance tuning
DS_TIMEOUT=60
DS_MAX_RETRIES=5
DS_DEFAULT_LIMIT=5000
```

Load with:

```python
from dotenv import load_dotenv
load_dotenv()

from delong_datasets import download_dataset
data = download_dataset("<dataset_id>", "your-token")
```

#### Runtime Configuration

```python
from delong_datasets import download_dataset, DownloadOptions

# Override defaults per-request
opts = DownloadOptions(
    timeout_sec=90,
    max_retries=10
)

data = download_dataset("<dataset_id>", "your-token", opts)
```
