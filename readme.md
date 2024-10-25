# ARCH: Knowledge Graph Creation from Clinical Features

## Overview

ARCH is a package designed for constructing a large-scale knowledge graph (KG) based on clinical feature co-occurrence matrices. The steps include data preprocessing, calculating co-occurrence matrices, variance estimation, and knowledge graph construction. The package applies methods introduced in the paper: 

**Gan, Ziming, et al. "Arch: Large-scale knowledge graph via aggregated narrative codified health records analysis." medRxiv (2023).**.

<img src="https://github.com/junwei-lu/ARCH/blob/main/img/arch.png"/>
## Prerequisites

- Python 3.x
- R (version X.X.X or later)
- Required Python libraries:
  - numpy
  - scipy
  - pandas
- Required R libraries:
  - Matrix
  - RSpectra


## Workflow

The process of constructing the knowledge graph involves several steps:

1. **Data Preprocessing**  
   Use the `pre.py` script to preprocess the raw dataset. This step will extract essential information such as:
   - Total number of patients
   - Average number of features recorded per patient per day

   Command to run:

   ```bash
   python pre.py
   ```

2. **Co-occurrence Matrix Calculation**  
   After preprocessing, use the `pre.R` script to calculate the co-occurrence matrix. This includes:
   - Calculating the SVD-SPPMI (Singular Value Decomposition of Shifted Positive Pointwise Mutual Information) matrix
   - Estimating parameters needed to calculate the variance of the SVD-SPPMI matrix

   Command to run in R:

   ```R
   Rscript pre.R
   ```

3. **Variance Estimation**  
   To estimate the variance of the calculated SVD-SPPMI matrix, use the `cosine_test.py` script:

   ```bash
   python cosine_test.py
   ```

4. **Knowledge Graph Construction**  
   Once the variance has been estimated, use the `construct_KG.R` script to construct the knowledge graph based on the processed data:

   ```R
   Rscript construct_KG.R
   ```

# Usage

Below is an example of how to use the ARCH package to create a knowledge graph (KG) from electronic health record (EHR) data. This example assumes that you have already preprocessed your data and calculated the necessary co-occurrence matrices.

## Example: Constructing the Knowledge Graph

### Step 1: Load Required Libraries and Data

Ensure you have all necessary libraries loaded and data files ready before running the script.

```r
# Load required packages
library(Matrix)
library(RSpectra)

# Load your preprocessed data (replace 'data_path' with the actual path to your data)
cooccurrence_matrix <- readRDS('data_path/cooccurrence_matrix.rds')
svd_sppmi_matrix <- readRDS('data_path/svd_sppmi_matrix.rds')
```

### Step 2: Perform Matrix Factorization
Use Singular Value Decomposition (SVD) to decompose the co-occurrence matrix. This will help reduce the dimensionality of the data and prepare it for knowledge graph construction.
```r
# Perform SVD on the co-occurrence matrix
svd_result <- svds(cooccurrence_matrix, k = 50)  # k is the number of dimensions

# Extract the U, D, and V matrices from the SVD result
U <- svd_result$u
D <- diag(svd_result$d)
V <- svd_result$v
```
### Step 3: Construct the Knowledge Graph
Now, you can construct the knowledge graph by using the SVD results. The graph can be represented as a set of relationships between clinical features based on their similarity in the lower-dimensional space.

```r
# Construct a similarity matrix based on the SVD results
similarity_matrix <- U %*% D %*% t(V)

# Set a threshold for determining strong relationships (adjust this based on your analysis)
threshold <- 0.8
knowledge_graph <- similarity_matrix > threshold
```
