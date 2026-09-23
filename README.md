# Sleep, Stress and Occupation: An Unsupervised Analysis of the Sleep Health and Lifestyle Dataset

An unsupervised analysis of the [Sleep Health and Lifestyle dataset](https://www.kaggle.com/datasets/uom190346a/sleep-health-and-lifestyle-dataset) (374 records, 13 attributes): clustering is performed **without ever showing the diagnostic label (Sleep Disorder)** to any algorithm, then the resulting groups are compared post-hoc against the diagnosis to see whether unsupervised structure has anything to do with it. A Bayesian Network is also learned to model conditional dependencies between all variables, including the diagnosis.


## Overview

- **Clustering.** Three techniques, agglomerative hierarchical clustering (Complete and Ward linkage), k-means (random and k-means++ init) and DBSCAN, are each run on the full 21-dimensional feature space and on an 8-component PCA-reduced space, for 10 configurations total. Compared via internal criteria (silhouette, WSS/BSS) and external criteria against the (held-out) diagnosis (ARI, V-measure, purity).
- **Bayesian Network.** A Directed Acyclic Graph is learned over all variables (including Sleep Disorder as a node) with Hill Climbing (BIC score), then cross-checked against a constraint-based structure (PC algorithm), to model which variables carry information about the diagnosis and through which paths.

## Key results

| Feature space | Method | k | Silhouette | ARI | Purity | V-measure |
|---|---|---|---|---|---|---|
| Full (372 pts) | Ward | 11 | 0.736 | 0.140 | 0.855 | 0.332 |
| Full (372 pts) | DBSCAN | 11 (+79 noise) | 0.962† | 0.183 | 0.928 | 0.415 |
| PCA (8 comp.) | **Ward** (selected) | 11 | **0.770** | 0.140 | 0.858 | 0.313 |
| PCA (8 comp.) | Complete | 8 | 0.595 | **0.224** | 0.863 | **0.352** |

† computed only on non-noise points, not directly comparable with the others.

Even though no algorithm ever saw the diagnosis, agreement with it is well above chance in every configuration (ARI up to 0.224, purity up to 0.93) — though never strong enough to reproduce it outright. The Bayesian Network recovers a physiological chain `BMI → sleep quality → sleep duration → stress → heart rate`, with stress and sleep quality informing the diagnosis only indirectly, through systolic pressure.

Full methodology, all ten configurations, the DAG figures and the discussion are in [`Docs/Report.pdf`](Docs/Report.pdf).

## Repository structure

```
.
├── Notebooks/
│   ├── helper.ipynb      # Shared preprocessing, clustering and evaluation utilities
│   └── main.ipynb        # End-to-end analysis: clustering, model selection, Bayesian network
├── Data/
│   └── Sleep_health_and_lifestyle_dataset.csv
├── Docs/
│   ├── Report.pdf
│   └── Sleep_Health_and_Lifestyle.pptx
├── requirements.txt
└── README.md
```

## Getting started

1. Clone the repository (or open the notebooks in Google Colab).
2. Install dependencies: `pip install -r requirements.txt`.
3. Make sure `Sleep_health_and_lifestyle_dataset.csv` is accessible at the path set in `main.ipynb` (update the data path variable if running locally instead of on Colab/Drive).
4. Run `helper.ipynb` first to load the shared functions, then run `main.ipynb` end to end.

## Requirements

See [`requirements.txt`](requirements.txt). Core stack: scikit-learn for clustering and metrics, `pgmpy` for Bayesian network structure learning and inference, `kneed` for the DBSCAN epsilon estimation, plus standard data/plotting libraries.

## Authors

- Angelica Iseni
- Kevin Del Gaudio

## References

- Sleep Health and Lifestyle Dataset — [Kaggle](https://www.kaggle.com/datasets/uom190346a/sleep-health-and-lifestyle-dataset)
- Ester, M., Kriegel, H.-P., Sander, J., Xu, X. *A Density-Based Algorithm for Discovering Clusters in Large Spatial Databases with Noise.* KDD, 1996.
- Breunig, M. M., Kriegel, H.-P., Ng, R. T., Sander, J. *LOF: Identifying Density-Based Local Outliers.* SIGMOD, 2000.
- Ankan, A., Panda, A. *pgmpy: Probabilistic Graphical Models using Python.* https://pgmpy.org
- Pedregosa, F. et al. *Scikit-learn: Machine Learning in Python.* JMLR 12, 2011.
