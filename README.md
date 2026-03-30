# GeoEmbeddings — Geographic Ratemaking with Spatial Embeddings

### Rethinking Risk: From Tabular Variables to Spatial Signals

For the modern actuary, Artificial Intelligence is often framed merely as a "faster tool" for optimization. However, the true paradigm shift lies in how AI—specifically Deep Learning—allows us to **perceive, delineate, and decompose risk**. 

While traditional actuarial models like **Generalized Linear Models (GLMs)** or **Gradient Boosting Machines (GBMs)** operate by identifying relationships between discrete, predefined features, Convolutional Neural Networks (CNNs) introduce a fundamental structural shift. They treat data not as a static table, but as a **signal to be decomposed and recomposed**. 

In the context of spatial risk, a CNN does not just "look" at a neighboring tract; it extracts hierarchical features—capturing textures, continuities, and gradients of socioeconomic or environmental variables. This allows for the capture of nuances that traditional distance-based kernels or fixed territorial smoothing often miss. As noted by **Blier-Wong et al. (2021)**, this approach enables the "automatic extraction of spatial features," moving from a manual craft of territory definition toward a high-dimensional, data-driven representation of geographic information.

---

## Project Overview

This repository provides a real-data implementation of the **Blier-Wong et al. (2021)** framework, applied to Florida flood insurance risk using National Flood Insurance Program (NFIP) data.

The core innovation is the construction of a **Geographic Data Square Cuboid (GDSC)**. Instead of treating a census tract as an isolated point, we treat it as the center of a local "image" of risk. By using a **CNN Autoencoder**, we learn to decompose the complex spatial fabric of Florida into a dense, 16-dimensional embedding ($z \in \mathbb{R}^{16}$) that captures the latent essence of the territory.

### The Methodology

1.  **Signal Construction (GDSC):** For each Florida census tract, a $7 \times 7 \times 100$ tensor is built from neighboring tracts, with each cell populated by ~100 ACS census variables.
2.  **Spatial Decomposition (CNN Encoder):** A CNN architecture learns to recognize patterns in the spatial distribution of data, compressing the high-dimensional cuboid into a meaningful embedding.
3.  **Actuarial Integration:** These embeddings are used to enhance a **Poisson GLM** for flood claim rate prediction, combining the predictive power of Deep Learning with the statistical rigor and interpretability required in actuarial practice.

```text
GDSC tensor (7 × 7 × ~100 census vars)
        │
   CNN Encoder (Feature Decomposition)
        │
  Embedding z ∈ ℝ¹⁶  +  Traditional features
        │
   Poisson GLM  →  λ̂ (claim rate)

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
