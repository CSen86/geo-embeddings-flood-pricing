# GeoEmbeddings — Geographic Ratemaking with Spatial CNN Embeddings

> Replicating and extending **Blier-Wong et al. (2021)** using Florida NFIP flood insurance data.

---

## Overview

Traditional actuarial pricing models treat each territory as an isolated point. This project asks a different question: *what if we treat a census tract as the centre of a spatial image, and let a neural network learn what that neighbourhood looks like?*

We build a **Geographic Data Square Cuboid (GDSC)** — a 7×7×63 tensor of ACS census features arranged around each Florida census tract — then train a **CNN Autoencoder** to compress it into a 16-dimensional embedding. Those embeddings replace (and outperform) hand-crafted territorial variables in a Poisson GLM for flood claim frequency.

```
GDSC tensor  (7 × 7 × 63 census features)
      │
 CNN Encoder  →  z ∈ ℝ¹⁶  (spatial embedding)
      │
 Poisson GLM  →  λ̂  (expected claim frequency per policy-year)
```

---

## Repository Structure

```
geo-embeddings-flood-pricing/
├── notebooks/
│   ├── 01_didactic_simulated.ipynb   # Self-contained walkthrough on synthetic data
│   └── 02_florida_nfip_real_data.ipynb  # Full pipeline on real NFIP + ACS data
├── data/
│   └── README.md                     # How to populate (files are gitignored)
├── docs/                             # Slides, paper drafts, figures
├── .gitignore
├── requirements.txt
└── README.md
```

---

## Notebooks

| Notebook | Purpose | Data needed |
|----------|---------|-------------|
| `01_didactic_simulated.ipynb` | Pedagogical walkthrough — GDSC construction, CNN autoencoder, GLM comparison — using 500 simulated tracts. Zero external downloads required. | None |
| `02_florida_nfip_real_data.ipynb` | Full replication on 5,160 Florida census tracts. Downloads NFIP claims/policies from FEMA OpenFEMA and ACS features from the Census Bureau API. | Internet + optional Census API key |

---

## Key Results (Notebook 02)

| Model | Features | Test MSE | vs M1 |
|-------|----------|----------|-------|
| M1 — Census GLM | 12 ACS covariates | 3,455 | baseline |
| M2 — Embedding GLM | 16 CNN embeddings | 2,678 | **−22.5%** |
| M3 — Combined GLM | Census + embeddings | 2,519 | **−27.1%** |

CNN autoencoder: 193:1 compression ratio (7×7×63 = 3,087 → 16 dims), 75,727 parameters.

---

## Setup

```bash
pip install -r requirements.txt
```

For notebook 02, a free Census API key speeds up ACS downloads but is not required:  
https://api.census.gov/data/key_signup.html

Set `CENSUS_API_KEY = "your_key"` in the configuration cell. Leave blank for keyless (rate-limited) mode.

---

## Data Sources

| Dataset | Source |
|---------|--------|
| NFIP Redacted Claims v2 | [FEMA OpenFEMA](https://www.fema.gov/openfema-data-page/fima-nfip-redacted-claims-v2) |
| NFIP Redacted Policies v2 | [FEMA OpenFEMA](https://www.fema.gov/openfema-data-page/fima-nfip-redacted-policies-v2) |
| ACS 5-Year Data Profiles (2022) | [Census Bureau API](https://api.census.gov) |

Data files are excluded from version control. See `data/README.md`.

---

## Reference

Blier-Wong, C., Cossette, H., Lamontagne, L., & Marceau, E. (2021).  
*Machine learning in P&C insurance: A review for pricing and reserving.*  
**ASTIN Bulletin**, 51(2), 351–378.
