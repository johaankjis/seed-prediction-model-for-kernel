# Seeds Dataset Documentation

## Overview

The Seeds dataset contains measurements of geometrical properties of kernels belonging to three different varieties of wheat: **Kama**, **Rosa**, and **Canadian**. This dataset is commonly used for classification and clustering tasks in machine learning.

## Dataset Statistics

- **Total Instances**: 209
- **Number of Features**: 7 (all continuous)
- **Number of Classes**: 3
- **Missing Values**: None
- **File Format**: Tab-separated text file

## Class Distribution

| Class | Wheat Variety | Number of Instances | Percentage |
|-------|---------------|---------------------|------------|
| 1 | Kama | ~70 | ~33.5% |
| 2 | Rosa | ~70 | ~33.5% |
| 3 | Canadian | ~69 | ~33.0% |

The dataset is fairly balanced across all three classes.

## Feature Descriptions

### 1. Area
- **Description**: The area of the wheat kernel
- **Type**: Continuous (real-valued)
- **Unit**: Square millimeters (mm²)
- **Range**: Approximately 10.59 - 21.18
- **Importance**: Larger kernels may indicate specific varieties or growing conditions

### 2. Perimeter
- **Description**: The perimeter of the wheat kernel
- **Type**: Continuous (real-valued)
- **Unit**: Millimeters (mm)
- **Range**: Approximately 12.41 - 17.25
- **Importance**: Related to kernel shape and size

### 3. Compactness
- **Description**: Compactness coefficient calculated as: C = 4πA/P²
  - Where A is area and P is perimeter
- **Type**: Continuous (real-valued)
- **Unit**: Dimensionless
- **Range**: Approximately 0.8081 - 0.9183
- **Importance**: Measures how close to a perfect circle the kernel is; higher values indicate rounder shapes

### 4. Length of Kernel
- **Description**: Length of the kernel (longest dimension)
- **Type**: Continuous (real-valued)
- **Unit**: Millimeters (mm)
- **Range**: Approximately 4.899 - 6.675
- **Importance**: Major axis measurement; key differentiator between varieties

### 5. Width of Kernel
- **Description**: Width of the kernel (shortest dimension)
- **Type**: Continuous (real-valued)
- **Unit**: Millimeters (mm)
- **Range**: Approximately 2.630 - 4.033
- **Importance**: Minor axis measurement; complements length for shape analysis

### 6. Asymmetry Coefficient
- **Description**: Asymmetry coefficient of the kernel
- **Type**: Continuous (real-valued)
- **Unit**: Dimensionless
- **Range**: Approximately 0.7651 - 8.456
- **Importance**: Measures deviation from symmetry; irregular kernels have higher values

### 7. Length of Kernel Groove
- **Description**: Length of the groove on the kernel
- **Type**: Continuous (real-valued)
- **Unit**: Millimeters (mm)
- **Range**: Approximately 4.519 - 6.550
- **Importance**: Characteristic feature visible on wheat kernels; varies by variety

### 8. Class Label
- **Description**: The variety of wheat kernel
- **Type**: Categorical (integer encoded)
- **Values**: 
  - 1 = Kama
  - 2 = Rosa
  - 3 = Canadian
- **Importance**: Target variable for supervised learning; ground truth for evaluation

## Data Collection

The measurements were obtained from digital images of wheat kernels using computer vision techniques. High-quality kernel images were processed to extract geometrical properties automatically.

### Measurement Process
1. Kernels were placed on a contrasting background
2. Digital images were captured under controlled lighting
3. Image processing algorithms identified kernel boundaries
4. Geometrical properties were calculated from the segmented regions
5. Multiple measurements were averaged for accuracy

## Feature Correlations

Key observations about feature relationships:

- **Area and Perimeter**: Strongly correlated (larger kernels have longer perimeters)
- **Length and Width**: Positively correlated but distinct (varies by kernel shape)
- **Compactness**: Inversely related to elongation (rounder vs. elongated kernels)
- **Asymmetry**: More independent; captures unique shape characteristics
- **Groove Length**: Moderate correlation with overall kernel size

## Data Quality

### Strengths
- Balanced class distribution
- No missing values
- Precise measurements from automated image analysis
- Consistent measurement methodology
- Real-world agricultural data

### Considerations
- Limited to three wheat varieties
- Measurements from specific growing conditions
- Possible measurement errors from image processing
- No temporal or geographic metadata

## Use Cases

This dataset is suitable for:

1. **Classification Tasks**
   - Supervised learning (SVM, Decision Trees, Neural Networks)
   - Multi-class classification benchmarking
   - Feature selection studies

2. **Clustering Analysis**
   - Unsupervised learning (KMeans, Hierarchical clustering)
   - Cluster validation techniques
   - Pattern discovery

3. **Dimensionality Reduction**
   - PCA (Principal Component Analysis)
   - t-SNE visualization
   - Feature importance analysis

4. **Educational Purposes**
   - Introduction to machine learning
   - Data preprocessing and visualization
   - Model evaluation and comparison

## File Format

The dataset is stored in `seeds_dataset.txt` as a tab-separated file without headers.

### Format Specification
```
<area>\t<perimeter>\t<compactness>\t<length>\t<width>\t<asymmetry>\t<groove>\t<class>
```

### Example Rows
```
15.26	14.84	0.871	5.763	3.312	2.221	5.22	1
12.37	13.47	0.8567	5.204	2.96	3.919	5.001	3
18.72	16.19	0.8977	6.006	3.857	5.324	5.879	2
```

## Loading the Dataset

### Using Pandas
```python
import pandas as pd

cols = ["area", "perimeter", "compactness", "length", "width", "asymmetry", "groove", "class"]
df = pd.read_csv("seeds_dataset.txt", names=cols, sep="\s+")
print(df.head())
print(df.describe())
```

### Using NumPy
```python
import numpy as np

data = np.loadtxt("seeds_dataset.txt")
X = data[:, :-1]  # Features
y = data[:, -1]   # Labels
```

### Using scikit-learn
```python
import pandas as pd
from sklearn.model_selection import train_test_split

cols = ["area", "perimeter", "compactness", "length", "width", "asymmetry", "groove", "class"]
df = pd.read_csv("seeds_dataset.txt", names=cols, sep="\s+")

X = df.drop('class', axis=1)
y = df['class']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)
```

## Data Preprocessing Recommendations

### 1. Scaling/Normalization
Different features have different ranges. Consider standardization:
```python
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
```

### 2. Outlier Detection
Check for outliers using box plots or statistical methods:
```python
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(15, 10))
for i, col in enumerate(cols[:-1], 1):
    plt.subplot(3, 3, i)
    sns.boxplot(y=df[col])
    plt.title(f'{col} Distribution')
plt.tight_layout()
plt.show()
```

### 3. Feature Engineering
Consider creating new features:
- Aspect ratio (length/width)
- Area-to-perimeter ratio
- Shape index combining multiple features

## Citation

If you use this dataset in your research, please acknowledge its source appropriately.

## References

- UCI Machine Learning Repository
- M. Charytanowicz, J. Niewczas, et al., "A Complete Gradient Clustering Algorithm for Features Analysis of X-ray Images"

## Additional Resources

- [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/)
- [Wheat Classification Research Papers](https://scholar.google.com/)
- [Computer Vision in Agriculture](https://www.sciencedirect.com/topics/agricultural-and-biological-sciences/computer-vision)

---

**Last Updated**: 2024
**Dataset Version**: 1.0
**Maintained by**: Repository Contributors
