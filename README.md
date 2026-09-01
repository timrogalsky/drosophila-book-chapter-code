# Drosophila Cell-Dynamics Unsupervised Machine Learning Workflow

This repository contains the reproducible computational workflow for an unsupervised machine-learning analysis of epithelial cell-area dynamics during sex-comb development in *Drosophila melanogaster*.

The project analyzes short, irregular time series from 131 epithelial cells observed across three time-lapse microscopy movies. After applying the predefined missing-data filter, 108 cells are retained for clustering and downstream analysis.

## Relationship to the published study

This repository builds on the published proceedings paper:

> Rogalsky, T., Campbell-Enns, L., Dyck, M., Malagon, N. **Robust Unsupervised Classification of Drosophila Cell Dynamics Using Dynamic Time Warping and Consensus Clustering of Engineered Features.** In *Emerging Trends in Computational Biology, Biomedical Engineering, and Health Informatics*, CSCE 2025, Communications in Computer and Information Science, vol. 2935, pp. 13–27. Springer (2026).
> https://doi.org/10.1007/978-3-032-22199-5_2

The published paper remains the record of the original analysis.

During preparation of an extended book-chapter manuscript, the computational workflow was audited, corrected, and fully rerun. The present repository contains that corrected workflow. In particular:

- DTW distances are computed using the overlapping valid developmental interval for each pair of cells, without imputing missing endpoints.
- DTW-based Gaussian mixture modeling is not included in the corrected primary consensus analysis.
- The production analysis evaluates six clustering assignment streams across the two retained resolutions. At the primary k=2 resolution, all six streams are audited, while strict and majority consensus use DTW-Ward, DTW-K-means, PCA-K-means, NMF-Ward, and NMF-K-means; PCA-Ward is excluded from the k=2 consensus because of its sensitivity to the number of retained principal components. At the secondary k=3 resolution, the complete six-stream audit is retained, while the stability-qualified strict set uses DTW-Ward, DTW-K-means, PCA-K-means, NMF-Ward, and NMF-K-means; PCA-Ward is excluded from the strict set because of its sensitivity to the number of retained principal components.
- The secondary k=3 analysis uses a strong recurrent core and a broader recurrent tier of exact five-stream signatures for focused downstream feature characterization.

The extended chapter manuscript is being prepared for the Springer Nature edited volume *Large Language Models: AI Tools, NLP and Use Cases*, in the series *Transactions on Computational Science and Computational Intelligence*.

## Repository structure

```text
.
├── 01_Preprocessing.ipynb
├── 02_DTW_Clustering.ipynb
├── 03_Feature_Engineering.ipynb
├── 04_Feature_Correlation_and_Selection.ipynb
├── 05_PCA_and_Clustering.ipynb
├── 06_NMF_and_Clustering.ipynb
├── 07_Cluster_Agreement.ipynb
├── 08_k2_Feature_Characterization.ipynb
├── 09_k3_Strict_Signature_Feature_Analysis.ipynb
├── data/
│   ├── raw/
│   └── processed/
├── figures/
├── requirements.txt
└── PRODUCTION_AUDIT.md
```

The nine numbered notebooks constitute the production workflow and must be run in numerical order, with a fresh kernel for each notebook.

The public release contains only the validated production workflow and outputs; diagnostic and revalidation provenance is retained separately in the private project archive.

Current production outputs are stored in `data/processed/`.

## Computational workflow

### 01. Preprocessing

Loads the original delta-area dataset, applies the missing-data criterion, and creates the normalized delta-area datasets used for downstream analysis.

Raw inputs are stored in:

```text
data/raw/
```

Generated datasets are written to:

```text
data/processed/
```

### 02. DTW clustering

Computes pairwise dynamic time warping distances over shared valid developmental intervals and performs hierarchical clustering using DTW dissimilarity-profile vectors.

The production workflow retains Ward hierarchical assignments at the primary k=2 and secondary k=3 resolutions, and exports DTW-profile K-means assignments at both k=2 and k=3 for cross-method agreement and consensus analyses.

### 03. Feature engineering

Generates interpretable statistical, frequency-domain, peak/trough, magnitude, autocorrelation, early/late, and oscillation-shape features from the cell time series.

The corrected workflow yields 55 usable engineered features.

### 04. Feature correlation and selection

Evaluates redundancy among engineered features and removes highly correlated features according to the documented selection rules.

The resulting production feature set contains 45 features.

### 05. PCA and clustering

Applies principal component analysis to the corrected feature matrix and performs both hierarchical and K-means clustering.

The production workflow retains 12 principal components.

### 06. NMF and clustering

Applies non-negative matrix factorization and evaluates rank sensitivity before performing hierarchical and K-means clustering on the retained representation.

The production workflow uses NMF rank 5.

### 07. Cluster agreement

Audits cross-method agreement at the primary k=2 and secondary k=3 resolutions using the Rand Index, normalized mutual information, and adjusted Rand Index. The primary k=2 consensus is then constructed from the five retained consensus streams, while the secondary k=3 analysis identifies exact five-pipeline intersection signatures.

At k=3, four strong recurrent signatures contain 54 of the 108 retained cells, while a broader recurrent tier of eight signatures contains 81 cells. These tiers are defined from the stability-qualified five-stream signature analysis and are carried forward for focused characterization.

### 08. k=2 feature characterization

Characterizes the unanimous and majority k=2 consensus groups across the 45 retained engineered features and records stage-to-stage robustness.

### 09. k=3 recurrent-signature feature analysis

Characterizes the stability-qualified k=3 signature structure across all 45 retained engineered features using:

- Kruskal-Wallis global tests
- rank eta-squared effect sizes
- Benjamini-Hochberg false-discovery-rate correction across the 45 global tests
- Dunn pairwise tests with Holm adjustment

The notebook analyzes both the four-signature strong recurrent core (54 cells) and the broader eight-signature recurrent tier (81 cells), compares feature-effect rankings across the two tiers, and produces the final k=3 feature-characterization tables and Figure 5 boxplots.

## Reproducing the analysis

The workflow was validated using Python 3.12.

Create or activate a Python environment and install the required packages:

```bash
python -m pip install -r requirements.txt
```

Run the notebooks from the repository root in numerical order:

```text
01_Preprocessing.ipynb
02_DTW_Clustering.ipynb
03_Feature_Engineering.ipynb
04_Feature_Correlation_and_Selection.ipynb
05_PCA_and_Clustering.ipynb
06_NMF_and_Clustering.ipynb
07_Cluster_Agreement.ipynb
08_k2_Feature_Characterization.ipynb
09_k3_Strict_Signature_Feature_Analysis.ipynb
```

Use a fresh kernel for each notebook. Notebook outputs may consume files produced by earlier notebooks, but no in-memory kernel state is shared between stages.

All repository paths are relative to the repository root. Intermediate and final production tables are written to `data/processed/`, and generated production plots are written to `figures/`. The repository includes the validated production outputs from the final revalidation run, allowing the reported results to be inspected without rerunning the notebooks.

## Licensing

Repository source code is licensed under the MIT License; see `LICENSE`.

The released dataset `data/raw/Delta cell area.xlsx` is licensed under the Creative Commons Attribution 4.0 International License (CC BY 4.0); see `DATA_LICENSE.md`.

The absolute cell-area dataset `Cell Area.xlsx` is not part of the public release.

## Authors

Tim Rogalsky<br>
Nicolas Malagon<br>
Lia Campbell-Enns<br>
Matthaeus Dyck

Canadian Mennonite University, Winnipeg, Manitoba, Canada

## Citation

For the validated software release accompanying the extended book-chapter analysis, cite:

Rogalsky, T., Malagon, N., Campbell-Enns, L., & Dyck, M. (2026). *Drosophila Cell-Dynamics Unsupervised Machine Learning Workflow* (Version 1.0.0). Zenodo. https://doi.org/10.5281/zenodo.22217053

For the published proceedings analysis, please cite the paper listed above.

Citation information for the extended book chapter will be added after publication.
