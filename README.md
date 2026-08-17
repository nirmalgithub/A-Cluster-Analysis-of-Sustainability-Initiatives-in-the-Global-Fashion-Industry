# Fashion Sustainability Cluster Analysis — Complete Reproducible Pipeline
## Business Analytics Dissertation | Traditional Research

This package contains everything needed to reproduce every figure, table, and
statistic reported in the dissertation from raw data through to the final
K=2/K=3/K=4 cluster analysis.

---

## Data Pipeline — How the Dataset Was Actually Built

The dataset behind this dissertation was assembled in four stages: two
manual data-preparation steps, followed by three scripts. This is stated
plainly here because the pipeline is not a single unbroken chain of
scripts — being explicit about which steps were manual and which were
scripted is part of the transparency this repository is meant to provide.

| Stage | What Happened | How |
|-------|----------------|-----|
| 1 | Downloaded the raw Fashion Transparency Index 2024 export from WikiRate (wikirate.org); the 79 binary Yes/No metrics were manually encoded (Yes=1, No=0), grouped into 6 sustainability themes, and converted into theme score percentages, then combined with CDP Climate Change Scores (looked up individually per company at cdp.net) into `Fashion_Dataset.csv` | Manual |
| 2 | Merged `Fashion_Dataset.csv` with the SBTi registry (`SBTI_Data.xlsx`), Remake 2021 scores, and HQ Region/Sub-segment tags into the master dataset | **`script_01_merge_sources.py`** |
| 3 | Added individual company performance data (Scope 1+2/3 emissions, renewable energy %, etc.) for 8 companies with available sustainability reports | **`script_02_add_performance_data.py`** |
| 4 | Ran K-means and hierarchical clustering, generated all 10 dissertation figures | **`script_03_full_clustering_analysis.py`** |

**If you only want to regenerate the dissertation figures**, you can skip
straight to Stage 4 — `Fashion_Sustainability_Final_Dataset.csv` is already
included in this package, fully merged through Stage 3, and ready to use
directly with `script_03_full_clustering_analysis.py`.

---

## Raw Input Files — Included

Unlike many reproducibility packages, the raw third-party inputs to
`script_01` are **included directly in this repository**, not just
referenced:

- **`Fashion_Dataset.csv`** — the manually prepared WikiRate+CDP starting
  file (64 companies), produced by Stage 1 above. This is the direct
  input to `script_01`.
- **`SBTI_Data.xlsx`** — the full SBTi registry bulk download (14,531
  companies) from sciencebasedtargets.org, used by `script_01` for
  name-matching against the 64-company sample. Of the 64 companies, 46
  were successfully matched in the registry; the remaining 18 are
  documented as a limitation in the dissertation (Chapter 3, Section 3.5).

This means `script_01` can be run exactly as provided, with no missing
files, to reproduce `Fashion_Sustainability_Final_Dataset.csv` from
scratch.

---

## How to Run the Main Analysis

### Requirements
```bash
pip install pandas scikit-learn scipy matplotlib seaborn numpy openpyxl
```

### Run
```bash
python script_03_full_clustering_analysis.py
```

### What it produces
All outputs are saved to an `outputs/` folder created automatically:

| Figure | Content |
|--------|---------|
| `fig1_elbow_silhouette.png` | Elbow method + silhouette scores, K=2 to K=8 |
| `fig2_pca_cluster_map.png` | PCA 2D scatter — primary K=2 solution |
| `fig3_radar_charts.png` | Radar charts — K=2 cluster theme profiles |
| `fig4_greenwashing_matrix.png` | Disclosure vs CDP greenwashing matrix |
| `fig5_dendrogram.png` | Hierarchical clustering dendrogram (Ward) |
| `fig6_subsegment_region.png` | Sub-segment / region heatmaps |
| `fig7_silhouette_plot.png` | Per-company silhouette coefficients |
| `fig8_k3_k4_pca.png` | Supplementary K=3 and K=4 PCA scatter plots |
| `fig9_cluster_comparison_table.png` | K=2/3/4 comparison table |
| `fig10_k3_k4_radars.png` | Supplementary K=3/K=4 radar charts |

The script also prints a full console log of every statistic reported in
the dissertation (silhouette scores, cluster sizes, cluster means, ARI,
greenwashing quadrant counts) so you can verify the numbers directly.

---

## Verified Reproducibility

This exact script was tested end-to-end and confirmed to reproduce every
number reported in Chapter 4 of the dissertation:

- **Primary K=2**: Silhouette = 0.3557
- **Hierarchical validation**: Silhouette = 0.358 (approx), ARI vs K-means = 0.6002
- **K=3 supplementary**: Silhouette = 0.2693 (Leaders n=11, Transitional n=17, Low Maturity n=36)
- **K=4 supplementary**: Silhouette = 0.2446 (Leaders n=11, Committed/Transitioning n=12, Emerging n=21, Low Maturity n=20)
- **Greenwashing matrix**: Genuine Leaders=22, Greenwashing Risk=10, Underreporters=9, Genuine Laggards=23

---

## Key Methodological Notes

- K-means is applied to **8 numeric variables**: the 6 continuous WikiRate
  theme score percentages (0–100), the CDP numeric score (0–7, continuous),
  and the binary SBTi net-zero indicator (z-score normalised). It is **not**
  applied to the raw 79 binary metrics directly — see Chapter 3, Section 3.4
  of the dissertation for the full justification (Hair et al., 2019).
- CDP "Not Disclosed" is encoded as **0** (below the lowest D rating of 1),
  on the basis that non-disclosure is itself informative rather than
  missing data at random.
- Greenwashing matrix thresholds: disclosure = **sample median** (18.5%),
  CDP = **score 4** (B rating — CDP's own minimum for "coordinated climate
  action").
- Cluster labels (Leaders/Developing/etc.) are assigned by ranking clusters
  on mean Overall Disclosure Score — this ensures consistent, reproducible
  labelling regardless of the arbitrary cluster index K-means assigns
  internally.
- Sub-segment and HQ Region are used **only for post-hoc profiling**, never
  as clustering inputs — this avoids circularity in the sub-segment/region
  gap analysis reported in Chapter 4, Section 4.5.

---

## File Manifest

```
final_package/
├── README.md                                  (this file)
├── script_01_merge_sources.py / .ipynb         (pipeline stage 2)
├── script_02_add_performance_data.py / .ipynb  (pipeline stage 3)
├── script_03_full_clustering_analysis.py / .ipynb  (★ pipeline stage 4 — main analysis)
├── Fashion_Dataset.csv                         (raw input — WikiRate+CDP, manually prepared)
├── SBTI_Data.xlsx                              (raw input — SBTi registry bulk download)
├── Fashion_Sustainability_Final_Dataset.csv    (merged master dataset, 64 companies)
└── figures/                                    (all 10 dissertation figures, PNG)
```

---

## Data Licensing and Attribution

This project uses data from the following sources, in compliance with their respective licences:

- **WikiRate / Fashion Revolution — Fashion Transparency Index 2024**: Licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Attribution: Fashion Revolution (2024) *Fashion Transparency Index 2024*. WikiRate.
- **CDP Climate Change Scores**: Publicly available data via [cdp.net](https://www.cdp.net).
- **Science Based Targets initiative (SBTi) Registry**: Publicly available via [sciencebasedtargets.org](https://sciencebasedtargets.org).
- **Remake Fashion Accountability Report 2021**: Publicly available via [remake.world](https://remake.world).

All code in this repository is original work produced for a Business Analytics dissertation and is provided for academic transparency and reproducibility purposes.

## Author

Nirmal Kumar Kannan — MSc Business Analytics Sept 2025,  Dissertation, University of Greenwich, August 2026.
