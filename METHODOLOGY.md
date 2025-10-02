# Methodology Documentation

This document provides a detailed explanation of the machine learning methodology used in the Seed Prediction Model project.

## Table of Contents

- [Overview](#overview)
- [Data Preprocessing](#data-preprocessing)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Clustering Analysis](#clustering-analysis)
- [Dimensionality Reduction](#dimensionality-reduction)
- [Model Evaluation](#model-evaluation)
- [Results Interpretation](#results-interpretation)

## Overview

The Seed Prediction Model employs **unsupervised learning** techniques to identify patterns in wheat kernel measurements. The primary goal is to demonstrate how geometrical properties can be used to distinguish between different wheat varieties without using labeled data during training.

### Key Techniques

1. **KMeans Clustering**: Unsupervised clustering algorithm
2. **Principal Component Analysis (PCA)**: Dimensionality reduction technique
3. **Data Visualization**: Scatter plots and comparative analysis

### Workflow

```
Data Loading → EDA → 2D Clustering → 7D Clustering → PCA → Evaluation → Visualization
```

## Data Preprocessing

### 1. Data Loading

```python
cols = ["area", "perimeter", "compactness", "length", "width", "asymmetry", "groove", "class"]
df = pd.read_csv("seeds_dataset.txt", names=cols, sep="\s+")
```

**Key Steps:**
- Read tab-separated text file
- Assign column names
- Handle whitespace-separated values
- No missing values to impute

### 2. Data Inspection

```python
# Basic statistics
print(df.describe())

# Check data types
print(df.dtypes)

# Check for missing values
print(df.isnull().sum())

# Class distribution
print(df['class'].value_counts())
```

### 3. Feature Preparation

```python
# Separate features from labels
X = df[cols[:-1]].values  # All columns except 'class'
y = df['class'].values     # Target variable
```

**Why this approach?**
- KMeans requires numerical features only
- Class labels are kept for evaluation purposes
- NumPy arrays provide efficient computation

## Exploratory Data Analysis

### 1. Pairwise Feature Relationships

The project generates all possible scatter plots between feature pairs:

```python
for i in range(len(cols)-1):
    for j in range(i+1, len(cols)-1):
        x_label = cols[i]
        y_label = cols[j]
        sns.scatterplot(x=x_label, y=y_label, data=df, hue="class")
        plt.show()
```

**Purpose:**
- Identify linear and non-linear relationships
- Detect potential separability between classes
- Discover which feature combinations are most discriminative

### 2. Key Observations

From the scatter plots:
- **Compactness vs. Asymmetry**: Shows good class separation
- **Area vs. Perimeter**: Strong positive correlation (expected)
- **Length vs. Width**: Moderate correlation, varies by variety
- Some feature pairs show overlapping classes

## Clustering Analysis

### Phase 1: 2D Clustering

#### Feature Selection

```python
x = "compactness"
y = "asymmetry"
X = df[[x, y]].values
```

**Why these features?**
- Initial exploration suggests they provide good separation
- Easier to visualize in 2D space
- Represents different aspects (shape vs. symmetry)

#### KMeans Application

```python
from sklearn.cluster import KMeans

kmeans = KMeans(n_clusters=3)
kmeans.fit(X)
clusters = kmeans.labels_
```

**Algorithm Parameters:**
- `n_clusters=3`: Matches the number of wheat varieties
- Default initialization: k-means++ (smart centroid initialization)
- Default max_iter: 300
- Default convergence tolerance: 1e-4

**How KMeans Works:**
1. Randomly initialize 3 cluster centroids
2. Assign each point to nearest centroid
3. Update centroids as mean of assigned points
4. Repeat steps 2-3 until convergence

#### Visualization

```python
cluster_df = pd.DataFrame(
    np.hstack((X, clusters.reshape(-1, 1))),
    columns=[x, y, "class"]
)
sns.scatterplot(x=x, y=y, hue="class", data=cluster_df)
```

**Comparison:**
- Plot 1: KMeans clustering results
- Plot 2: Actual class labels
- Visual inspection reveals clustering accuracy

### Phase 2: Higher Dimensional Clustering

#### Using All Features

```python
X = df[cols[:-1]].values  # All 7 features
kmeans = KMeans(n_clusters=3).fit(X)
```

**Advantages:**
- Utilizes complete information
- Captures patterns not visible in 2D
- Expected to improve clustering quality

**Challenges:**
- Cannot directly visualize 7D space
- Risk of curse of dimensionality
- Requires dimensionality reduction for visualization

#### Performance Comparison

```python
# Visualize high-D clustering in 2D projection
cluster_df = pd.DataFrame(
    np.hstack((X, kmeans.labels_.reshape(-1, 1))),
    columns=df.columns
)

# Use same 2D features for comparison
sns.scatterplot(x="compactness", y="asymmetry", hue="class", data=cluster_df)
```

## Dimensionality Reduction

### Principal Component Analysis (PCA)

PCA is used to reduce the 7-dimensional feature space to 2 dimensions while preserving maximum variance.

#### Mathematical Foundation

PCA finds orthogonal axes (principal components) that capture the most variance in the data:

1. Center the data (subtract mean)
2. Compute covariance matrix
3. Find eigenvectors and eigenvalues
4. Project data onto top-k eigenvectors

#### Implementation

```python
from sklearn.decomposition import PCA

pca = PCA(n_components=2)
X_pca = pca.fit_transform(X)
```

**Parameters:**
- `n_components=2`: Reduce to 2 dimensions for visualization
- Automatic scaling and centering

**Interpretation:**
- PC1 (First Principal Component): Direction of maximum variance
- PC2 (Second Principal Component): Direction of second-most variance
- These are linear combinations of original features

#### Variance Explained

```python
print(f"Variance explained by PC1: {pca.explained_variance_ratio_[0]:.2%}")
print(f"Variance explained by PC2: {pca.explained_variance_ratio_[1]:.2%}")
print(f"Total variance explained: {sum(pca.explained_variance_ratio_):.2%}")
```

**Why PCA?**
- Reduces dimensionality for visualization
- Removes correlation between features
- Highlights most important patterns
- Preserves global structure

### Visualization in PCA Space

#### KMeans Results

```python
kmeans_pca_df = pd.DataFrame(
    np.hstack((X_pca, kmeans.labels_.reshape(-1, 1))),
    columns=["pca1", "pca2", "class"]
)
sns.scatterplot(x="pca1", y="pca2", hue='class', data=kmeans_pca_df)
```

#### Actual Classes

```python
truth_pca_df = pd.DataFrame(
    np.hstack((X_pca, df["class"].values.reshape(-1, 1))),
    columns=["pca1", "pca2", "class"]
)
sns.scatterplot(x="pca1", y="pca2", hue='class', data=truth_pca_df)
```

## Model Evaluation

### Visual Evaluation

Since this is unsupervised learning, evaluation is primarily visual:

1. **Cluster Compactness**: How tight are the clusters?
2. **Cluster Separation**: How distinct are different clusters?
3. **Agreement with Ground Truth**: How well do clusters match actual classes?

### Potential Quantitative Metrics

While not implemented in the current notebook, these metrics could be added:

#### Silhouette Score

Measures how similar an object is to its own cluster compared to other clusters:

```python
from sklearn.metrics import silhouette_score

score = silhouette_score(X, kmeans.labels_)
print(f"Silhouette Score: {score:.3f}")
```

- Range: [-1, 1]
- Higher is better
- >0.5 indicates good clustering

#### Adjusted Rand Index (ARI)

Measures agreement between clustering and ground truth:

```python
from sklearn.metrics import adjusted_rand_score

ari = adjusted_rand_score(df['class'], kmeans.labels_)
print(f"Adjusted Rand Index: {ari:.3f}")
```

- Range: [-1, 1]
- 1.0 indicates perfect agreement
- 0.0 indicates random labeling

#### Normalized Mutual Information

```python
from sklearn.metrics import normalized_mutual_info_score

nmi = normalized_mutual_info_score(df['class'], kmeans.labels_)
print(f"Normalized Mutual Information: {nmi:.3f}")
```

## Results Interpretation

### Clustering Performance

**2D Clustering (Compactness + Asymmetry):**
- ✓ Shows reasonable separation
- ✓ Captures some class structure
- ✗ Limited information (only 2 features)
- ✗ Some overlap between classes

**7D Clustering (All Features):**
- ✓ Uses complete information
- ✓ Better separation (expected)
- ✓ More robust to feature noise
- ✗ Cannot directly visualize

**PCA Visualization:**
- ✓ Reveals global structure
- ✓ Shows natural groupings
- ✓ Preserves most variance
- ✗ Linear transformation only
- ✗ Interpretation of PCs is complex

### Key Findings

1. **Feature Importance**: Some features are more discriminative than others
2. **Class Overlap**: Rosa and Canadian varieties show more overlap
3. **PCA Effectiveness**: 2 components capture significant variance
4. **KMeans Performance**: Reasonable clustering without supervision

### Limitations

1. **KMeans Assumptions**:
   - Assumes spherical clusters
   - Sensitive to initialization
   - Requires specifying k (number of clusters)

2. **PCA Limitations**:
   - Linear transformation only
   - May lose important non-linear patterns
   - PCs may not align with interpretable features

3. **Dataset Constraints**:
   - Limited to 3 varieties
   - Specific growing conditions
   - No temporal or spatial information

## Future Improvements

### Algorithm Enhancements

1. **Try different clustering algorithms**:
   - DBSCAN (density-based)
   - Hierarchical clustering
   - Gaussian Mixture Models

2. **Optimize hyperparameters**:
   - Use Elbow method for k
   - Cross-validation for robustness
   - Multiple random initializations

3. **Add supervised learning**:
   - Compare with SVM, Random Forest
   - Train/test split for evaluation
   - Feature selection techniques

### Analysis Depth

1. **Feature engineering**:
   - Create polynomial features
   - Domain-specific transformations
   - Feature interactions

2. **Advanced visualization**:
   - 3D scatter plots
   - Interactive plots with Plotly
   - t-SNE for non-linear reduction

3. **Statistical validation**:
   - Hypothesis testing
   - Confidence intervals
   - Bootstrap sampling

## References

1. **KMeans Clustering**:
   - MacQueen, J. (1967). "Some methods for classification and analysis of multivariate observations"
   - scikit-learn documentation: [KMeans](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html)

2. **Principal Component Analysis**:
   - Pearson, K. (1901). "On lines and planes of closest fit to systems of points in space"
   - scikit-learn documentation: [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html)

3. **Evaluation Metrics**:
   - Rousseeuw, P. J. (1987). "Silhouettes: a graphical aid to the interpretation and validation of cluster analysis"
   - Hubert, L., & Arabie, P. (1985). "Comparing partitions"

## Conclusion

This methodology demonstrates a complete unsupervised learning workflow from data exploration to model evaluation. The approach successfully identifies patterns in wheat kernel measurements and provides insights into variety classification without using labeled data during training.

---

**Author**: Project Contributors  
**Last Updated**: 2024  
**Version**: 1.0
