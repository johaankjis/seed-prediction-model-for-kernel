# Quick Start Guide

Get up and running with the Seed Prediction Model in 5 minutes!

## 🚀 Fast Track Setup

### 1. Clone the Repository

```bash
git clone https://github.com/johaankjis/seed-prediction-model-for-kernel.git
cd seed-prediction-model-for-kernel
```

### 2. Install Dependencies

```bash
pip install pandas numpy matplotlib seaborn scikit-learn jupyter
```

### 3. Launch Jupyter Notebook

```bash
jupyter notebook
```

### 4. Open and Run

Open `Seed_Prediction_Model_for_identifying_type_of_kernel.ipynb` and run all cells!

---

## 📊 What You'll See

### Step 1: Data Loading
The notebook loads wheat kernel measurements with 7 features:
- Area, Perimeter, Compactness, Length, Width, Asymmetry, Groove

### Step 2: Exploratory Data Analysis
Beautiful scatter plots showing relationships between all feature pairs, colored by wheat variety.

### Step 3: 2D Clustering
KMeans clustering using just 2 features (compactness & asymmetry):
- Left plot: KMeans results
- Right plot: Actual classes
- Compare to see how well unsupervised learning works!

### Step 4: 7D Clustering
Clustering using all 7 features for better accuracy.

### Step 5: PCA Visualization
Dimensionality reduction from 7D to 2D:
- Easier to visualize
- Preserves most important patterns
- Compare clustering vs. ground truth

---

## 💡 Quick Examples

### Load and Explore Data

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load data
cols = ["area", "perimeter", "compactness", "length", "width", "asymmetry", "groove", "class"]
df = pd.read_csv("seeds_dataset.txt", names=cols, sep="\s+")

# Quick look
print(df.head())
print(df.describe())

# Visualize one feature
sns.boxplot(data=df, x='class', y='compactness')
plt.title('Compactness by Wheat Variety')
plt.show()
```

### Run KMeans Clustering

```python
from sklearn.cluster import KMeans

# Prepare features
X = df[cols[:-1]].values

# Cluster
kmeans = KMeans(n_clusters=3, random_state=42)
predictions = kmeans.fit_predict(X)

# Check results
print(f"Cluster centers:\n{kmeans.cluster_centers_}")
```

### Apply PCA

```python
from sklearn.decomposition import PCA

# Reduce dimensions
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X)

# Visualize
plt.scatter(X_pca[:, 0], X_pca[:, 1], c=df['class'], cmap='viridis')
plt.xlabel('First Principal Component')
plt.ylabel('Second Principal Component')
plt.colorbar(label='Wheat Variety')
plt.title('PCA Visualization')
plt.show()

# Variance explained
print(f"Variance explained: {pca.explained_variance_ratio_}")
```

---

## 🎯 Common Tasks

### Compare Clustering Algorithms

```python
from sklearn.cluster import KMeans, DBSCAN, AgglomerativeClustering
import numpy as np

X = df[cols[:-1]].values

# Try different algorithms
kmeans = KMeans(n_clusters=3, random_state=42)
dbscan = DBSCAN(eps=0.5, min_samples=5)
hierarchical = AgglomerativeClustering(n_clusters=3)

# Get predictions
kmeans_labels = kmeans.fit_predict(X)
dbscan_labels = dbscan.fit_predict(X)
hierarchical_labels = hierarchical.fit_predict(X)

# Visualize
from sklearn.decomposition import PCA
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X)

fig, axes = plt.subplots(1, 3, figsize=(15, 4))

axes[0].scatter(X_pca[:, 0], X_pca[:, 1], c=kmeans_labels, cmap='viridis')
axes[0].set_title('KMeans')

axes[1].scatter(X_pca[:, 0], X_pca[:, 1], c=dbscan_labels, cmap='viridis')
axes[1].set_title('DBSCAN')

axes[2].scatter(X_pca[:, 0], X_pca[:, 1], c=hierarchical_labels, cmap='viridis')
axes[2].set_title('Hierarchical')

plt.tight_layout()
plt.show()
```

### Evaluate Clustering Quality

```python
from sklearn.metrics import silhouette_score, adjusted_rand_score

# Silhouette score (higher is better)
silhouette = silhouette_score(X, kmeans_labels)
print(f"Silhouette Score: {silhouette:.3f}")

# Compare with ground truth
ari = adjusted_rand_score(df['class'], kmeans_labels)
print(f"Adjusted Rand Index: {ari:.3f}")
```

### Feature Importance with PCA

```python
from sklearn.decomposition import PCA
import pandas as pd

# Apply PCA
pca = PCA()
pca.fit(X)

# Create component DataFrame
components_df = pd.DataFrame(
    pca.components_,
    columns=cols[:-1],
    index=[f'PC{i+1}' for i in range(len(cols)-1)]
)

print("Feature contributions to principal components:")
print(components_df.round(3))

# Plot variance explained
plt.figure(figsize=(10, 4))
plt.subplot(1, 2, 1)
plt.bar(range(1, 8), pca.explained_variance_ratio_)
plt.xlabel('Principal Component')
plt.ylabel('Variance Explained')
plt.title('Scree Plot')

plt.subplot(1, 2, 2)
plt.plot(range(1, 8), np.cumsum(pca.explained_variance_ratio_), marker='o')
plt.xlabel('Number of Components')
plt.ylabel('Cumulative Variance Explained')
plt.title('Cumulative Variance')
plt.grid(True)
plt.tight_layout()
plt.show()
```

---

## 🔧 Troubleshooting

### Issue: "ModuleNotFoundError"

**Solution:** Install missing package
```bash
pip install <package_name>
```

### Issue: "Jupyter not opening"

**Solution:** Try alternative launch
```bash
jupyter notebook --no-browser
# Then open the URL shown in terminal
```

### Issue: "Plots not showing"

**Solution:** Add magic command in first cell
```python
%matplotlib inline
```

### Issue: "Data file not found"

**Solution:** Ensure you're in the correct directory
```bash
pwd  # Check current directory
ls   # Should see seeds_dataset.txt
```

---

## 📚 Next Steps

Once you're comfortable with the basics:

1. **Read [METHODOLOGY.md](METHODOLOGY.md)** for detailed explanations
2. **Explore [DATASET.md](DATASET.md)** to understand the features
3. **Check [CONTRIBUTING.md](CONTRIBUTING.md)** to add your own features
4. **Try different algorithms** (SVM, Random Forest, Neural Networks)
5. **Add evaluation metrics** (accuracy, precision, recall)
6. **Create your own visualizations**

---

## 🆘 Need Help?

- **Questions about the code?** Check the notebook comments
- **Want to contribute?** See [CONTRIBUTING.md](CONTRIBUTING.md)
- **Found a bug?** Open an issue on GitHub
- **Need clarification?** Read [README.md](README.md) for full documentation

---

## 🎓 Learning Resources

### KMeans Clustering
- [scikit-learn KMeans Guide](https://scikit-learn.org/stable/modules/clustering.html#k-means)
- [Understanding KMeans](https://www.youtube.com/watch?v=4b5d3muPQmA)

### PCA (Principal Component Analysis)
- [StatQuest: PCA](https://www.youtube.com/watch?v=FgakZw6K1QQ)
- [scikit-learn PCA Guide](https://scikit-learn.org/stable/modules/decomposition.html#pca)

### Data Visualization
- [Seaborn Tutorial](https://seaborn.pydata.org/tutorial.html)
- [Matplotlib Gallery](https://matplotlib.org/stable/gallery/index.html)

---

**Happy Learning! 🌾📊🚀**
