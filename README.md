# SIGNAL data and analysis

Data collection, cleaning, and exploratory analysis for [SIGNAL](https://signal.augustineantonyjoy.com).

## Included CVE snapshot

[`cve_raw.csv`](cve_raw.csv) contains the exact NVD snapshot used in this analysis, with selected fields extracted by [`fetch_cve_data.ipynb`](fetch_cve_data.ipynb). It is a tabular export, not the complete API JSON response. Re-running the API query can produce different records or scores because NVD updates its data.

- Source: [NVD Vulnerabilities API](https://nvd.nist.gov/developers/vulnerabilities)
- Endpoint: `https://services.nvd.nist.gov/rest/json/cves/2.0`
- Requested publication window: June 1, 2026 at 00:00:00 through September 18, 2026 at 00:00:00 (the end is midnight at the start of September 18).
- Query parameters: `pubStartDate=2026-06-01T00:00:00.000`, `pubEndDate=2026-09-18T00:00:00.000`, `resultsPerPage=200`, with `startIndex` incremented by 200 for pagination.
- Rows: **40,519**; records without a CVSS score: **5,460**.
- Fields: `cve_id`, `published`, `description`, `cvss_score`, `cvss_severity`, `cvss_version`, `cwe`.
- File size: 22,993,421 bytes.
- Collection timestamp: not recorded by the original notebook; the preserved snapshot and checksum identify the exact data used.
- SHA-256: `36d3fd07923ac71d27fbc35c4cf92d038e4b14ecbd1d8b7e7afd19a4202d36ee`.

[Download the CSV snapshot](https://raw.githubusercontent.com/augustineantonyjoy/signal-data/main/cve_raw.csv).

## CIC-IDS2017 traffic data

Download the machine-learning CSV files from the [official CIC-IDS2017 dataset page](https://www.unb.ca/cic/datasets/ids-2017.html). Original traffic files and locally generated CSVs remain excluded from Git. The CVE snapshot is the only CSV exception.

To reproduce the traffic preparation:

1. Extract the eight traffic CSV files into `~/Downloads/MachineLearningCVE`, or change `CSV_FOLDER` in [`load_clean_cicids2017.ipynb`](load_clean_cicids2017.ipynb) to your extracted folder.
2. Run that notebook from the repository directory. It installs pandas and NumPy, combines the files, strips column-name whitespace, converts infinite values to missing values, drops incomplete rows and exact duplicates, and repairs broken attack-label characters.
3. The notebook writes `cicids2017_cleaned.csv` and a 5,000-row `cicids2017_raw_sample.csv` selected with `random_state=1`. The sample is taken after column-name whitespace has been stripped.
4. Run [`eda_visualizations.ipynb`](eda_visualizations.ipynb) using the generated cleaned traffic file and the committed `cve_raw.csv`. Existing chart outputs and dataset previews are in [`figures/`](figures/).

Use a Python/Jupyter environment and run notebook cells in order. Generating the cleaned traffic data requires enough memory to load and combine the source files.

## Refreshing CVE data

For reproducing the published analysis, use the committed snapshot. Run `fetch_cve_data.ipynb` only when deliberately collecting a new snapshot: its final cell overwrites `cve_raw.csv` in the current directory.

The notebook reads an optional API key from the `NVD_API_KEY` environment variable. Set it locally before starting Jupyter; do not put a key in notebook source or commit credentials. Without a key, the notebook uses a slower request interval. Updating the snapshot should also update the row count, checksum, collection timestamp, and analysis outputs documented here.
