# GeoEmbeddings — Geographic Ratemaking with Spatial Embeddings

A real-data implementation of the **Blier-Wong et al. (2021)** approach applied to Florida flood insurance (NFIP).

**Reference:** Blier-Wong, Cossette, Lamontagne & Marceau (2021). *Geographic Ratemaking with Spatial Embeddings.* ASTIN Bulletin, 52(1), 1–31. [DOI: 10.1017/asb.2021.25](https://doi.org/10.1017/asb.2021.25)

**Author:** Claudio Senatore Reso

---

## Overview

For each Florida census tract, a **7×7 Geographic Data Square Cuboid (GDSC)** is built from neighboring tracts, each cell populated with ~100 ACS census variables. A **CNN autoencoder** learns 16-dimensional spatial embeddings from these cuboids. The embeddings are then used to enhance a Poisson GLM for flood claim rate prediction.

```
GDSC tensor (7 × 7 × ~100 census vars)
        │
   CNN Encoder
        │
  Embedding z ∈ ℝ¹⁶  +  Traditional features
        │
   Poisson GLM  →  λ̂ (claim rate)
```

## Data Sources

| File | Source | Description |
|------|--------|-------------|
| `claims_fl.parquet` | FEMA OpenFEMA — NFIP Redacted Claims v2 | Flood claim counts per census tract (2018–2022) |
| `policies_fl.parquet` | FEMA OpenFEMA — NFIP Redacted Policies v2 | Policy exposure per tract |
| `acs_fl.parquet` | U.S. Census Bureau — ACS 5-Year (2022) | ~100 socioeconomic features per tract |
| `gaz_fl.parquet` | Census Bureau — Gazetteer Files | Tract centroids (lat/lon) |

> Data files are excluded from version control (see `.gitignore`). Download instructions are in the notebook.

## Project Structure

```
GeoEmbeddings/
├── notebooks/
│   └── GeoEmbeddings_Florida_NFIP_GDSC.ipynb
├── data/                   # excluded from git
│   ├── acs_fl.parquet
│   ├── claims_fl.parquet
│   ├── gaz_fl.parquet
│   └── policies_fl.parquet
├── .gitignore
├── requirements.txt
└── README.md
```

## Setup

```bash
pip install -r requirements.txt
```

You will also need a free **Census API key**: https://api.census.gov/data/key_signup.html

Set it in the notebook configuration section (`CENSUS_API_KEY`). Keyless mode is supported but subject to lower rate limits.

## Model Architecture

- **Grid size:** 7×7 neighbors per tract
- **Grid step:** 3.0 km between grid points
- **Latent dimension:** 16
- **NFIP window:** 2018–2022 (matching ACS period)
- **State:** Florida (FIPS 12)
