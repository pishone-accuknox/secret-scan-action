# AccuKnox Secret Scan GitHub Action

This GitHub Action scans your repository for secrets and uploads the result to your tenet in AccuKnox SaaS.

## Learn More

- [About Accuknox](https://www.accuknox.com/)

---

## Inputs

| **Input Name**  | **Description** | **Optional/Required** | **Default Value** |
|-----------------|-----------------|-----------------------|-------------------|
| `token` | The token for authenticating with the CSPM panel. | Required | `None` |
| `tenant_id` | The ID of the tenant. | Required | `None` |
| `label` | The label created in AccuKnox SaaS. | Required | `None` |
| `endpoint` | The URL of the CSPM panel to push the scan results to. | Required | `cspm.demo.accuknox.com` |
| `results` | Specifies which type(s) of results to output: `verified`, `unknown`, `unverified`, `filtered_unverified`. Defaults to all types. | Optional | `all` |
| `fail` | Fail the pipeline if secrets are found. | Optional | `false` |
| `exclude-paths` | Paths to exclude from the scan. | Optional | `None` |
| `args` | Additional arguments to pass to TruffleHog. | Optional | `None` |

---

## Usage

```yaml
- name: Accuknox Secret Scan
    uses: accuknox/secret-scan-action@v1.0.0
    with:
    # The token for authenticating with the CSPM panel.
    # Required
    token: ${{ secrets.ACCUKNOX_TOKEN }}

    # The ID of the tenant associated with the CSPM panel.
    # Required
    tenant_id: ${{ secrets.ACCUKNOX_TENANT_ID }}

    # The label created in AccuKnox SaaS for associating scan results.
    # Required
    label: 'label-name'

    # The URL of the CSPM panel to push the scan results to.
    # Default: cspm.demo.accuknox.com
    # Required
    endpoint: ${{ secrets.ACCUKNOX_ENDPOINT }}

    # Specifies the type(s) of results to output. Accepts comma-separated values.
    # Example: unverified, filtered_unverified
    # Default: all
    # Optional
    results: "verified,unknown"

    # Fail the pipeline if secrets are found.
    # Accepts: true or false
    # Default: false
    # Optional
    fail: "true"

    # Path to a file containing regex patterns for excluding paths from the scan.
    # Example: exclude-paths.txt
    # Optional
    exclude-paths: "exclude-paths.txt"

    # Additional arguments to pass. Find the whole list down below.
    # Example: "--archive-timeout=ARCHIVE-TIMEOUT"
    # Optional
    args: "--include-detectors="all""
```
### List of available flags

```
All available Flags:
      --log-level=0         Logging verbosity on a scale of 0 (info) to 5 (trace). Can be disabled with "-1".
      --concurrency=20           Number of concurrent workers.
      --no-verification     Don't verify the results.
      --allow-verification-overlap
                                 Allow verification of similar credentials across detectors
      --filter-unverified   Only output first unverified result per chunk per detector if there are more than one results.
      --verifier=VERIFIER ...    Set custom verification endpoints.
      --custom-verifiers-only   Only use custom verification endpoints.
      --archive-max-size=ARCHIVE-MAX-SIZE
                                 Maximum size of archive to scan. (Byte units eg. 512B, 2KB, 4MB)
      --archive-max-depth=ARCHIVE-MAX-DEPTH
                                 Maximum depth of archive to scan.
      --archive-timeout=ARCHIVE-TIMEOUT
                                 Maximum time to spend extracting an archive.
      --include-detectors="all"  Comma separated list of detector types to include. Protobuf name or IDs may be used, as well as ranges.
      --exclude-detectors=EXCLUDE-DETECTORS
                                 Comma separated list of detector types to exclude. Protobuf name or IDs may be used, as well as ranges. IDs defined here take precedence over the include list.
  -i, --include-paths=INCLUDE-PATHS
                                 Path to file with newline separated regexes for files to include in scan.
      --exclude-globs=EXCLUDE-GLOBS
                                 Comma separated list of globs to exclude in scan. This option filters at the `git log` level, resulting in faster scans.

---

## Minimalist Sample Configuration

```yaml
name: Accuknox Secret Scan
on: 
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  truffle:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          ref: ${{ github.head_ref }}
          
      - name: Accuknox Secret Scan
        uses: accuknox/secret-scan-action@v1.0.0
        with:
          endpoint: ${{ secrets.CSPM_ENDPOINT }}
          tenant_id: ${{ secrets.TENANT_ID }}
          label: ${{ vars.LABEL_ID }}
          token: ${{ secrets.CSPM_TOKEN }}
          fail: "false"
