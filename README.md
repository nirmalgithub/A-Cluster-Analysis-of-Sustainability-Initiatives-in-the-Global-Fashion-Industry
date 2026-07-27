# Fashion Sustainability Cluster Analysis — Complete Reproducible Pipeline
## Business Analytics Dissertation | Traditional Research

This package contains everything needed to reproduce every figure, table, and
statistic reported in the dissertation from raw data through to the final
K=2/K=3/K=4 cluster analysis.

---

## Pipeline Order

Run the scripts in this exact order:

| Step | Script | What It Does | Input | Output |
|------|--------|--------------|-------|--------|
| 1 | `script_01_wikirate_to_excel.py` | Pivots the raw WikiRate CSV export (long format) into a clean wide-format Excel file; encodes 79 binary Yes/No metrics as 1/0; calculates the 6 theme scores (Carbon, Energy, Supply Chain, Social, Governance, Materials) and the Overall Disclosure Score | Raw WikiRate CSV export (not included — see note below) | `Fashion_Sustainability_Dataset.xlsx` |
| 2 | `script_02_merge_sources.py` | Merges WikiRate+CDP data with the SBTi registry (name-matching + numeric encoding), adds Remake 2021 scores (manually transcribed) and manual HQ Region/Sub-segment tags | `Fashion_Dataset.csv` + `SBTI_Data.xlsx` (not included — see note) | `Fashion_Sustainability_Final_Dataset.csv` |
| 3 | `script_03_add_performance_data.py` | Adds `Perf_*` columns with individual company sustainability report data (Adidas, ASOS, AEO, Asics, Gap, Hugo Boss, Hanesbrands, Burberry) | `Fashion_Sustainability_Final_Dataset.csv` | `Fashion_Sustainability_Final_Dataset.csv` (updated in place) |
| 4 | **`script_04_full_clustering_analysis.py`** | **The main analysis script.** Runs K-means (K=2 to K=8), selects optimal K via elbow+silhouette, cross-validates with hierarchical clustering (Ward linkage), builds the greenwashing matrix, PCA visualisation, radar charts, sub-segment/regional heatmaps, and the K=3/K=4 supplementary analysis | `Fashion_Sustainability_Final_Dataset.csv` | 10 figures + `Fashion_Clustered_Dataset.csv` |

**If you only want to regenerate the dissertation figures**, you can skip
straight to Step 4 — `Fashion_Sustainability_Final_Dataset.csv` is already
included in this package fully merged and ready to use.

---

## ⚠️ Important Note on Steps 1–2 Raw Inputs

Scripts 01 and 02 reference two raw input files that are **not included** in
this package because they are the original user-collected/downloaded source
files, not generated outputs:

- **Raw WikiRate CSV export** — downloaded directly from wikirate.org
  (Fashion Transparency Index 2024, CC BY 4.0 licence). Re-download from
  WikiRate if you need to re-run Step 1 from scratch.
- **`SBTI_Data.xlsx`** — the full SBTi registry bulk download (14,531
  companies) from sciencebasedtargets.org.
- **`Fashion_Dataset.csv`** — the pre-merged WikiRate+CDP starting file
  (64 companies) used as the input to Step 2.

These scripts are included for **documentation and transparency** of the
full data pipeline, but you do not need to re-run them — the fully merged
output of Steps 1–3, `Fashion_Sustainability_Final_Dataset.csv`, is already
provided in this package. **Step 4 is the script you actually need to run
to regenerate every figure and statistic in the dissertation.**

---

## How to Run Step 4 (the main analysis)

### Requirements
```bash
pip install pandas scikit-learn scipy matplotlib seaborn numpy openpyxl
```

### Run
```bash
python script_04_full_clustering_analysis.py
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
| `Fashion_Clustered_Dataset.csv` | Final dataset with all cluster labels (K2/K3/K4) and greenwashing category |

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
├── requirements.txt                           (Python dependencies)
├── script_01_wikirate_to_excel.py / .ipynb     (raw data processing)
├── script_02_merge_sources.py / .ipynb         (multi-source merge)
├── script_03_add_performance_data.py / .ipynb  (company report data)
├── script_04_full_clustering_analysis.py / .ipynb  (★ main analysis script)
├── Fashion_Sustainability_Final_Dataset.csv    (merged master dataset, 64 companies)
├── Fashion_Clustered_Dataset.csv               (dataset + cluster assignments)
├── Fashion_Cluster_Input_Final.csv             (compact clustering-ready summary)
├── Performance_Data_Collection_Template.xlsx   (company report collection template)
└── figures/                                    (all 10 dissertation figures)
```
