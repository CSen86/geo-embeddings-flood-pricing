# Data Directory

Parquet files are **not tracked in git** (see `.gitignore`).

## How to populate this directory

Run the data-download cell at the top of either notebook:
- `notebooks/01_didactic_simulated.ipynb` — uses only simulated data, no download needed.
- `notebooks/02_florida_nfip_real_data.ipynb` — downloads from FEMA OpenFEMA and Census ACS APIs.

## Expected files after running notebook 02

| File | Description | Size (approx) |
|------|-------------|---------------|
| `nfip_florida_policies.parquet` | NFIP flood policies 2018–2022, Florida | ~80 MB |
| `nfip_florida_claims.parquet` | NFIP flood claims 2018–2022, Florida | ~15 MB |
| `acs_florida_dp02_dp05.parquet` | ACS 2022 Data Profiles DP02–DP05 for Florida census tracts | ~5 MB |

All files are gitignored. Add them manually or re-run the notebook download cells.
