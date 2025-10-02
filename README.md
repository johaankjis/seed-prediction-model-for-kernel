# Seed Prediction Model for Kernel Classification

A machine learning project that identifies the type of wheat kernel based on geometrical properties using unsupervised learning techniques.

[![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)](https://www.python.org/downloads/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-yellowgreen.svg)](https://scikit-learn.org/)

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Dataset](#dataset)
- [Installation](#installation)
- [Usage](#usage)
- [Methodology](#methodology)
- [Project Structure](#project-structure)
- [Results](#results)
- [Requirements](#requirements)
- [Contributing](#contributing)
- [License](#license)
- [Acknowledgments](#acknowledgments)

## 🔍 Overview

The Seed Prediction Model aims to categorize wheat kernels into three different varieties:
- **Kama** (Class 1)
- **Rosa** (Class 2)
- **Canadian** (Class 3)

This classification is based on seven real-valued geometrical attributes measured from kernel images. The project demonstrates the application of unsupervised machine learning techniques, including KMeans clustering and Principal Component Analysis (PCA), for pattern recognition in agricultural data.

## ✨ Features

- **Exploratory Data Analysis (EDA)**: Comprehensive visualization of feature relationships
- **KMeans Clustering**: Implementation of unsupervised clustering algorithm
- **Dimensionality Reduction**: PCA for reducing feature space from 7D to 2D
- **Comparative Analysis**: Visual comparison between clustering results and actual classes
- **Multi-dimensional Clustering**: Clustering in both reduced (2D) and full feature space (7D)
- **Interactive Visualizations**: Scatter plots and heatmaps for data exploration

## 📊 Dataset

The dataset contains **209 instances** of wheat kernels with the following attributes:

| Feature | Description | Type |
|---------|-------------|------|
| Area | Area of the kernel | Continuous |
| Perimeter | Perimeter of the kernel | Continuous |
| Compactness | Compactness coefficient | Continuous |
| Length | Length of kernel | Continuous |
| Width | Width of kernel | Continuous |
| Asymmetry | Asymmetry coefficient | Continuous |
| Groove | Length of kernel groove | Continuous |
| Class | Wheat variety (1, 2, or 3) | Categorical |

**Source**: The dataset is derived from measurements of geometrical properties of wheat kernels belonging to three different varieties.

For more detailed information about the dataset, see [DATASET.md](DATASET.md).

## 🛠️ Installation

### Prerequisites

- Python 3.7 or higher
- pip package manager
- Jupyter Notebook or JupyterLab (recommended)

### Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/johaankjis/seed-prediction-model-for-kernel.git
   cd seed-prediction-model-for-kernel
   ```

2. **Create a virtual environment (recommended)**
   ```bash
   python -m venv venv
   
   # On Windows
   venv\Scripts\activate
   
   # On macOS/Linux
   source venv/bin/activate
   ```

3. **Install required dependencies**
   ```bash
   pip install pandas numpy matplotlib seaborn scikit-learn jupyter
   ```

   Or using a requirements file (if available):
   ```bash
   pip install -r requirements.txt
   ```

## 🚀 Usage

### Running the Jupyter Notebook

1. **Launch Jupyter Notebook**
   ```bash
   jupyter notebook
   ```

2. **Open the main notebook**
   - Navigate to `Seed_Prediction_Model_for_identifying_type_of_kernel.ipynb`
   - Run cells sequentially (Cell → Run All) or individually

### Quick Start Example

```python
import pandas as pd
import numpy as np
from sklearn.cluster import KMeans
from sklearn.decomposition import PCA
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset
cols = ["area", "perimeter", "compactness", "length", "width", "asymmetry", "groove", "class"]
df = pd.read_csv("seeds_dataset.txt", names=cols, sep="\s+")

# Prepare features (exclude class label)
X = df[cols[:-1]].values

# Apply KMeans clustering
kmeans = KMeans(n_clusters=3, random_state=42)
kmeans.fit(X)

# Apply PCA for visualization
pca = PCA(n_components=2)
X_pca = pca.fit_transform(X)

# Visualize results
plt.figure(figsize=(10, 5))

plt.subplot(1, 2, 1)
plt.scatter(X_pca[:, 0], X_pca[:, 1], c=kmeans.labels_, cmap='viridis')
plt.title('KMeans Clustering Results')
plt.xlabel('First Principal Component')
plt.ylabel('Second Principal Component')

plt.subplot(1, 2, 2)
plt.scatter(X_pca[:, 0], X_pca[:, 1], c=df['class'], cmap='viridis')
plt.title('Actual Classes')
plt.xlabel('First Principal Component')
plt.ylabel('Second Principal Component')

plt.tight_layout()
plt.show()
```

## 🔬 Methodology

The project follows a structured approach to kernel classification:

### 1. Data Loading and Exploration
- Load the seeds dataset from text file
- Explore data structure and statistical properties
- Visualize feature relationships using scatter plots

### 2. 2D Clustering Analysis
- Select two features (compactness and asymmetry)
- Apply KMeans clustering with k=3
- Compare clustering results with actual classes

### 3. Higher Dimensional Clustering
- Use all 7 features for clustering
- Apply KMeans in the full feature space
- Visualize results in 2D projection

### 4. Principal Component Analysis
- Reduce dimensionality from 7D to 2D
- Preserve maximum variance in the data
- Visualize both clustering and actual classes in PCA space

### 5. Comparative Analysis
- Compare KMeans predictions with actual labels
- Evaluate clustering performance visually
- Analyze patterns and misclassifications

## 📁 Project Structure

```
seed-prediction-model-for-kernel/
│
├── README.md                                           # This file
├── DATASET.md                                          # Detailed dataset documentation
├── CONTRIBUTING.md                                     # Contribution guidelines
├── seeds_dataset.txt                                   # Raw dataset file
├── Seed_Prediction_Model_for_identifying_type_of_kernel.ipynb  # Main notebook
└── requirements.txt                                    # Python dependencies (optional)
```

## 📈 Results

The project demonstrates:

- **Effective Clustering**: KMeans successfully identifies patterns in the wheat kernel data
- **Dimensionality Reduction**: PCA preserves most variance while reducing from 7D to 2D
- **Visual Insights**: Clear separation between wheat varieties in the PCA space
- **Pattern Recognition**: Geometrical properties effectively distinguish wheat varieties

### Key Findings

1. Compactness and asymmetry are strong discriminative features
2. Using all 7 features improves clustering accuracy
3. PCA visualization reveals natural groupings in the data
4. Some overlap exists between Rosa and Canadian varieties

## 📦 Requirements

- **pandas** >= 1.0.0 - Data manipulation and analysis
- **numpy** >= 1.18.0 - Numerical computing
- **matplotlib** >= 3.1.0 - Data visualization
- **seaborn** >= 0.10.0 - Statistical data visualization
- **scikit-learn** >= 0.22.0 - Machine learning algorithms
- **jupyter** >= 1.0.0 - Interactive computing environment

## 🤝 Contributing

Contributions are welcome! Here's how you can help:

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/AmazingFeature`)
3. **Commit your changes** (`git commit -m 'Add some AmazingFeature'`)
4. **Push to the branch** (`git push origin feature/AmazingFeature`)
5. **Open a Pull Request**

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for details on our code of conduct and the process for submitting pull requests.

### Ideas for Contribution

- Add supervised learning models (SVM, Random Forest, Neural Networks)
- Implement cross-validation and model evaluation metrics
- Add hyperparameter tuning
- Create a web interface for predictions
- Add more visualization techniques
- Improve documentation and examples
- Add unit tests

## 📄 License

This project is open source and available under the MIT License.

## 🙏 Acknowledgments

- Dataset sourced from UCI Machine Learning Repository or similar agricultural research
- Inspired by practical applications of machine learning in agriculture
- Built with popular Python data science libraries
- Thanks to the open-source community for amazing tools

---

**Note**: This project is for educational and research purposes. For production use in agricultural applications, additional validation and testing are recommended.

## 📧 Contact

For questions, suggestions, or collaborations, please open an issue in this repository.

---

Made with ❤️ for advancing machine learning in agriculture
