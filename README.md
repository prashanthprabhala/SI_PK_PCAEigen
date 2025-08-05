## ASDRP PCA‐Eigen Repository

This is the repository containing the code associated with the work "Determining Key Pharmacokinetic Properties of FDA-Approved Therapeutic Drugs Using Machine Learning and Weighted Score Analysis". 

This project automates the end-to-end extraction, processing, and weighted-score analysis of pharmacokinetic predictions for FDA-approved small-molecule drugs, using three web-based ADMET platforms (SwissADME, XUNDrug, ADMETLab 2.0). It computes both eigenvalue- and IQR-based weights on standardized PK features to rank compounds and track temporal trends.

### Key Features

* **Automated Data Retrieval**
  * – **SwissADME/**: contains raw SMILES (`activeIng.csv`) and scripts to generate known-ADMET outputs.
  * – **XUNdrug/obtainRawData.py**: headless Selenium script that pulls predicted ADMET features into `raw_smiles.csv`.
  * – **AdmetLab2.0/**: `getraw.py` and `getRawOptimization.py` use Selenium + BeautifulSoup to scrape ADMETLab 2.0 predictions from `rawsmiles.csv`.

* **Data Processing & PCA**
  Each platform folder is subdivided into analysis stages:

  * **A/**: raw prediction table (`Raw*.csv`)
  * **C/**: covariance matrix (`covariance_matrix.csv`)
  * **S/**: standardized z-scores (`z_scores_output.csv`)
  * **Te/** or **B/**: eigenvalues (`eigen_output.csv`), explained variances (`Variance.csv`), PCA results (`PCA.csv`)
  * **summary\_output.csv**: final weighted-score ranking per active ingredient

* **Overall/**
  Consolidates each platform’s summaries for side-by-side comparison, temporal trend plots, and combined analyses.

* **Visualization (not included in code)**
  Matplotlib scripts (in notebooks or your own driver) can consume the summary files to re-create:

  * SMILES-length vs. response-time dot plots
  * Boxplots of execution-time distributions
  * Bar charts of top eigenvalues/IQRs per factor
  * Time-series of mean weighted scores by approval year

### Dependencies

* Python 3.8+
* pandas, numpy, scipy
* matplotlib, seaborn (for downstream plotting)
* selenium, webdriver-manager, beautifulsoup4

You can install the bulk of these with:

```bash
pip install pandas numpy scipy matplotlib seaborn selenium webdriver-manager beautifulsoup4
```

### Usage

1. **Prepare your SMILES list**

   * Populate each platform’s `rawsmiles.csv` (or `activeIng.csv` for SwissADME) with one SMILES string per row.

2. **Run data-extraction scripts**

   ```bash
   cd SwissADME
   # if there’s a dedicated scrape script, run it here

   cd ../XUNdrug
   python obtainRawData.py

   cd ../AdmetLab2.0
   python getraw.py              # baseline scrape
   python getRawOptimization.py  # optimized version
   ```

3. **Compute PCA & weights**
   For each platform folder, run your analysis driver (or sequentially):

   ```python
   # pseudocode / example
   import pandas as pd, numpy as np
   # 1. load Raw*.csv
   # 2. compute covariance matrix → save C/covariance_matrix.csv
   # 3. extract eigenvalues/vectors → save Te/eigen_output.csv
   # 4. standardize (z-scores) → save S/z_scores_output.csv
   # 5. compute weighted scores (eigen/IQR) → save summary_output.csv
   ```

4. **Aggregate & visualize**

   * Copy each `summary_output.csv` into `Overall/<platform>/`
   * Use your favorite plotting script or notebook to re-generate the figures from the paper.

### Verifying Accuracy

* **Sanity‐check raw data**: inspect the first few rows of each `Raw*.csv` against the live web interface.
* **Covariance & eigenvalues**: compare your computed covariance matrices and eigenvalues against the provided examples (e.g. `AdmetLab2.0/A/covariance_matrix.csv`).
* **Weighted‐score trends**: reproduce manuscript Figure 9 by plotting year‐of‐approval on the *x*-axis and mean weighted score on the *y*-axis; verify negative correlations and p-values match the paper.


