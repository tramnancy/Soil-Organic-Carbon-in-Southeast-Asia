# SOC-Southeast-Asia

[![R version](https://img.shields.io/badge/R-%3E%3D4.4.0-blue.svg)](https://www.r-project.org/)

This repository contains data, code, and analysis supporting the senior research thesis:

> **"Modeling Soil Organic Carbon (SOC) in Southeast Asia: Methods and Climate Implications"**
> Tram M. Tran — Senior Thesis 2026, Data Analytics Program & Department of Sustainability and Environmental Studies, Denison University.
> Research Advisors: Dr. Sarah Supp and Dr. Thomas Henshaw.

This study analyzes 2,709 soil organic carbon (SOC) observations across nine Southeast Asian countries and three soil depth classes (topsoil 0–30 cm, midsoil 30–60 cm, deepsoil 60+ cm) to understand the relative contributions of climate (temperature, precipitation) and land management (land-use type, fertilization, tillage) to SOC concentration. The analysis applies a convergent three-model framework: Random Forest (predictive importance), Linear Mixed-Effects Models (effect size and direction), and piecewise Structural Equation Modeling (direct and indirect pathway analysis). See Senior Thesis Paper at https://drive.google.com/file/d/1aYA0HHAN_9SJGpidRaaXtyuS3qj3U2vb/view?usp=sharing 

*Note: This repository is under active development (c) 2025–2026.*

---

## Code Authors

- **Tram M. Tran**, Data Analytics & Sustainability and Environmental Studies, Denison University ([tran_n3@denison.edu](mailto:trant@denison.edu))

## Research Advisors

- **Dr. Sarah R. Supp**, Denison University ([supps@denison.edu](mailto:supps@denison.edu))
- **Dr. Thomas Henshaw**, Denison University ([henshawt@denison.edu](mailto:henshawt@denison.edu))

---

## Getting Started

### Prerequisites

This project requires **R version 4.4.0 or greater**. R is freely available at [https://www.r-project.org/](https://www.r-project.org/).

The following R packages must be installed before running any analysis files:

| Package | Purpose |
| --- | --- |
| `dplyr`, `tidyr`, `stringr`, `readr` | Data wrangling |
| `sf`, `terra` | Spatial data handling and CHELSA raster extraction |
| `ggmap` | Study site map (requires API key; fallback included) |
| `ggplot2`, `patchwork` | Visualization and panel figures |
| `randomForest` | Random Forest models |
| `lme4`, `lmerTest` | Linear Mixed-Effects Models |
| `MuMIn` | Marginal and conditional R² for LMMs |
| `emmeans` | Estimated Marginal Means for land-use comparisons |
| `piecewiseSEM` | Piecewise Structural Equation Modeling |


---

## Data

### Primary SOC Dataset

This project uses the publicly available SOC dataset published by Gomez et al. (2024):

> Gomez, C., et al. (2024). A dataset for soil organic carbon in agricultural systems for the Southeast Asia region. *Scientific Data*. [https://doi.org/10.1038/s41597-024-03213-3](https://doi.org/10.1038/s41597-024-03213-3)
>
> Dataset available on Figshare: [https://doi.org/10.6084/m9.figshare.23736891](https://doi.org/10.6084/m9.figshare.23736891)

The dataset aggregates 4341 observations from 209 peer-reviewed articles across nine Southeast Asian countries (1987–2023). After applying the inclusion criteria, 2699 observations were used in this analysis.

### Climate Data

Mean annual temperature (bio1, °C) and mean annual precipitation (bio12, mm) were extracted from CHELSA v2.1 climatologies using site coordinates:

> Karger, D. N., et al. (2021). Climatologies at high resolution for the Earth's land surface areas. *EnviDat*. [https://doi.org/10.16904/envidat.228](https://doi.org/10.16904/envidat.228)

CHELSA rasters must be downloaded separately from [https://chelsa-climate.org/](https://chelsa-climate.org/) and placed in the `Data/CHELSA/` directory before running the analysis. See `Data/CHELSA/README_chelsa.txt` for required file names.

### Data Directory Structure

```
Data/
├── clean1.csv                  # Cleaned SOC dataset (from Gomez et al. 2024)
├── CHELSA/
│   ├── README_chelsa.txt       # Instructions for downloading CHELSA rasters
│   ├── CHELSA_bio1_1981-2010_V.2.1.tif   # Mean annual temperature (download separately)
│   └── CHELSA_bio12_1981-2010_V.2.1.tif  # Mean annual precipitation (download separately)
└── processed/
    ├── soc_main.csv            # Analysis-ready dataset (after cleaning and CHELSA merge)
    └── depth_subsets/
        ├── depth_030.csv       # Topsoil observations (0–30 cm, n = 1935)
        ├── depth_3060.csv      # Midsoil observations (30–60 cm, n = 351)
        └── depth_60.csv        # Deepsoil observations (60+ cm, n = 423)
```

---

## Code

The analysis is contained in a single reproducible R Markdown file that runs sequentially from raw data to final model outputs and figures.

| File | Description | Stage |
| --- | --- | --- |
| `SOC_SEA_analysis.Rmd` | Complete analysis pipeline: data import, cleaning, CHELSA extraction, depth classification, descriptive statistics, ANOVA, Random Forest, LMM, piecewiseSEM, and EMM estimation. All figures and model outputs are generated from this file. | Full pipeline |

### Steps to run the analysis

1. Before loading into GitHub and R, we used a Google Sheet to remove several irrelevant columns of the original dataset.
2. Download the Gomez et al. (2024) dataset from Figshare and place it at `Data/clean1.csv`. 
3. Download the two required CHELSA rasters (see `Data/CHELSA/README_chelsa.txt`) and place them in `Data/CHELSA/`.
4. Open `SOC_SEA_analysis.Rmd` in RStudio and click **Knit**, or run all chunks sequentially.

The file is structured in the following order:

```
Libraries and setup
1. Data import and cleaning
   1.1 Load raw SOC dataset
   1.2 Append CHELSA climate data
   1.3 Depth classification (midpoint method)
   1.4 SOC unit harmonization and log-transformation
   1.5 Build analysis-ready dataset (soc_main)
   1.6 Create management intensity variables (fert_intensity, tillage_intensity)
   1.7 Creating depth subsets
2. Descriptive statistics and exploratory analysis
   2.1 Study site map
   2.2 SOC summary statistics by depth class
   2.3 ANOVA: depth class effect on SOC
   2.4 Climate variable distributions
   2.5 SOC distribution by land use and depth
3. Random Forest: predictive importance
   3.1 Fit models (ntree = 500, set.seed(123))
   3.2 Model performance (R² and RMSE)
   3.3 Predicted vs. observed plot
   3.4 Variable importance plot
4. Linear Mixed-Effects Models
   4.1 Land-use models by depth (Agroforestry = reference)
   4.2 Fertilization models by depth
   4.3 Tillage models by depth
   4.4 Plot for all LMM fixed effects
5. Piecewise Structural Equation Modeling
   5.1 Fertilization pathways (0–30, 30–60, 60+ cm)
   5.2 Tillage pathways (only 0–30 cm converged)
6. Land-use effects: Estimated Marginal Means
   6.1 Compute EMMs for each depth
   6.2 Plot for EMMs across all depths
7. Session info
```

---

## Figures

All figures are generated directly by `SOC_SEA_analysis.Rmd` and saved to the `Figures/` directory. Key outputs include:

| Figure | Description |
| --- | --- |
| Figure 1 | Study site map across nine SEA countries |
| Figure 2 | SOC summary by depth class (ANOVA boxplot) |
| Figure 3 | Climate variable distributions (precipitation and temperature) |
| Figure 4 | Random Forest predicted vs. observed by depth|
| Figure 5 | Variable importance (%IncMSE) by depth class by depth |
| Figure 6 | LMM estimates with 95% CI by depth |
| Figure 7 | Piecewise SEM path diagrams - fertilization |
| Figure 8 | Piecewise SEM path diagrams - tillage pathways|
| Figure 9 | Estimated Marginal Means for land-use types by depth |

---

## Methods Summary

| Model | Purpose | Key predictors | R package |
| --- | --- | --- | --- |
| Random Forest | Variable importance ranking (%IncMSE) | Temperature, precipitation, management (secondary_descriptor) | `randomForest` |
| Linear Mixed-Effects Model | Fixed-effect estimates with 95% CI; country + year as random intercepts | Land use, fertilization, tillage, temperature, precipitation | `lme4`, `lmerTest` |
| Piecewise SEM | Direct and indirect pathway associations | Climate → management → SOC | `piecewiseSEM` |
| Estimated Marginal Means | Climate-adjusted land-use SOC comparisons | Land use (holding climate at mean) | `emmeans` |

All models were run separately for three depth levels: topsoil (0–30 cm), midsoil (30–60 cm), and deepsoil (60+ cm). Random Forest models used `set.seed(123)` for reproducibility.

---

## Key Findings

- **Topsoil (0–30 cm):** Precipitation is the dominant positive predictor of SOC across all three modeling frameworks. Fertilization is the only management practice with a significant positive association with SOC (Coefficient = +0.217, p < 0.001, approximately +24%). Land-use type does not significantly differentiate topsoil SOC once climate is controlled (LMM: Annual Coefficient = −0.016, non-significant; Perennial Coefficient = +0.003, non-significant; EMM overall p = 0.8).
- **Midsoil (30–60 cm):** Climate fixed effects lose significance. Annual cropping systems are associated with significantly lower SOC than agroforestry. Agroforestry shows the highest climate-adjusted EMM, substantially exceeding perennial and annual systems.
- **Deepsoil (60+ cm):** Both temperature (Coefficient = −0.547, p < 0.001) and precipitation (Coefficient = −0.452, p < 0.001) show significant negative associations with SOC. This is a directional reversal from the surface pattern consistent with climate-driven decomposition and leaching in warm, wet lowland soils.

This study provides the first convergent multi-method, multi-depth analysis of SOC drivers across the region and establishes a reproducible analytical template for future large-scale observational SOC research in tropical agroecosystems.
