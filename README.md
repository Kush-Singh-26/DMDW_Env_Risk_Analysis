# Air Quality Clustering Analysis

This project performs clustering analysis on the Indian Air Quality dataset (`city_day.csv`). The goal is to apply various clustering algorithms to identify and interpret distinct pollution profiles based on different chemical pollutants.

The workflow is divided into one data preparation notebook and four distinct clustering notebooks.

---
## Notebook Workflow

### 00. Data Cleaning and Feature Engineering

* **Purpose:** To clean the raw `city_day.csv` file and engineer new features to prepare the data for machine learning.
* **Process:**
    1.  **Handle Missing Data:** Drops the 'Xylene' column (over 60% missing), imputes other pollutant values using `KNNImputer`, and fills any remaining gaps with the global median.
    3.  **Feature Engineering:** Creates new time-based features (`Month`, `Is_Weekend`, `Season`) and a chemical ratio (`PM2.5_to_PM10_Ratio`).

### Common Steps in Clustering Notebooks (01-04)

All four clustering notebooks begin with the same steps:
1.  **Feature Selection:** Select 11 pollutant features (e.g., 'PM2.5', 'PM10', 'NO2', 'SO2', 'CO', etc.).
3.  **Scaling:** Apply `StandardScaler` to all features. It normalizes the data, giving each pollutant equal importance regardless of its original scale.
4.  **Visualization:** Use **PCA (Principal Component Analysis)** to reduce the 11 dimensions to 2. This is done *only for 2D visualization* of the clusters; the models themselves are trained on the full 11-dimensional scaled data.

### 01. K-Means Clustering

* K-Means is a **centroid-based** algorithm. It aims to partition data into *K* clusters, where each data point belongs to the cluster with the nearest mean (the *centroid*). It works by iteratively assigning points to the nearest centroid and then recalculating the centroid's position.
* **Implementation:**
    * The optimal number of clusters (K) is determined using the **Elbow Method**. This involves plotting the Sum of Squared Errors (SSE) for different values of K and finding the "elbow" point where the error stops decreasing rapidly.
    * The analysis identifies **K=4** as the optimal number.
    * The model is fit to the data, and the mean pollutant values for each of the 4 clusters are analyzed to understand their chemical profiles.

### 02. Agglomerative Clustering

* This is a **hierarchical (bottom-up)** clustering algorithm. It starts by treating each data point as its own cluster. Then, it iteratively merges the two "closest" clusters until only one cluster (containing all points) remains.
* **Implementation:**
    * A **Dendrogram** (a tree diagram) is created to visualize the hierarchical merging. The `linkage='ward'` method is used, which minimizes the variance of the clusters being merged.
    * By examining the dendrogram and identifying the largest distances between merges, an optimal cluster count of **K=4** is chosen.
    * The model is fit to produce 4 distinct clusters.

### 03. DBSCAN (Density-Based Spatial Clustering)

* DBSCAN is a **density-based** algorithm that groups together points that are closely packed, marking as **outliers (noise)** those points that lie alone in low-density regions. It is excellent at finding arbitrarily shaped clusters and does not require the number of clusters to be specified beforehand.
* **Implementation:**
    * The model is run by defining two parameters: `eps` (the maximum distance between two points to be considered neighbors) and `min_samples` (the minimum number of points to form a dense "core").
    * This model also explicitly identifies and separates **noise points** (labeled as cluster `-1`), which represent anomalous pollution days that don't fit well with any other group.

### 04. Gaussian Mixture Models (GMM)

* GMM is a **probabilistic (model-based)** algorithm. It assumes that the data points are generated from a mixture of a finite number of Gaussian distributions (bell curves). Unlike K-Means, it performs "soft clustering," calculating the *probability* that each point belongs to each cluster.
* **Implementation:**
    * The model is fit with `n_components=4`, assuming the data is a mixture of 4 distinct Gaussian distributions.
    * The model then assigns each data point to the cluster (distribution) it most likely belongs to ("hard clustering"). The mean pollutant values for each cluster are then analyzed.

---
## Conclusion: Algorithm Comparison

This table summarizes the key characteristics of the four clustering methods used in this analysis.

| Algorithm | Model Type | How 'K' is Determined | Key Feature |
| :--- | :--- | :--- | :--- |
| **K-Means** | Centroid-Based | **Elbow Method** (User-defined `K=4`) | Simple, fast, and assumes spherical clusters of similar size. |
| **Agglomerative** | Hierarchical | **Dendrogram** (User-defined `K=4`) | Creates a full hierarchy; good for understanding nested relationships. |
| **DBSCAN** | Density-Based | **Algorithm-Defined** (Based on `eps` & `min_samples`) | Can find non-spherical clusters and **explicitly identifies noise/outliers**. |
| **GMM** | Probabilistic | **User-Defined** (`n_components=4`) | Provides "soft" probabilities of cluster membership; flexible cluster shapes. |