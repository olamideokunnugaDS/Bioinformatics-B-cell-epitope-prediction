B-Cell Epitope Prediction for Chagas Disease

Project Overview
This project develops a machine learning pipeline to predict Linear B-cell epitopes for Trypanosoma cruzi, the protozoan parasite responsible for Chagas disease. Identifying B-cell epitopes is crucial for vaccine development, disease diagnosis, and prevention. This work demonstrates handling of severely imbalanced datasets, high-dimensional feature spaces, and the application of advanced ensemble methods.

The Challenge
The dataset presented several significant challenges:
Extreme class imbalance: Only 0.997% positive cases (332 positives vs 44,718 negatives)
High dimensionality: Multiple protein embedding features with significant multicollinearity
Group structure: Peptides sharing biological context requiring careful data splitting to prevent leakage
Non-linear separability: Minority class deeply embedded within majority class distribution

Methodology
Exploratory Data Analysis
Initial investigation revealed continuous float features with varying distributions and skewness, suggesting tree-based models would be most appropriate. Correlation analysis showed clusters of highly correlated features, indicating the need for potential dimensionality reduction strategies.

Data Preprocessing
Group-aware splitting: Used GroupShuffleSplit to prevent data leakage from peptides with common biological context
Outlier handling: Applied RobustScaler (median centering and IQR scaling) after detecting thousands of extreme values
Feature scaling: Centered features on median for direct magnitude comparison

Feature Engineering Experiments
Principal Component Analysis (PCA) was tested at 95% and 90% variance retention thresholds. However, empirical testing showed poor performance - the minority class was not linearly separable in the PCA-reduced feature space. Visualization confirmed the minority class was deeply embedded within the majority class cloud, leading to the decision to work with the full feature set and rely on non-linear ensemble methods.

Model Development
Initial Baselines:
Compared Random Forest classifiers with various configurations (SMOTE, class weights, with/without PCA)
XGBoost with SMOTE achieved balanced accuracies of 0.57-0.60, establishing a strong baseline
Advanced Resampling Comparison: Using GroupKFold with per-fold threshold optimization, I evaluated multiple sampling strategies:
SMOTE
ADASYN
SMOTE-Tomek
SMOTEENN
Cluster Centroids
Balanced Bagging
Balanced Bagging significantly outperformed others, achieving 0.77 balanced accuracy compared to 0.52-0.59 for other methods.

Final Optimized Pipeline: An ensemble of XGBoost estimators with Balanced Bagging underwent extensive hyperparameter tuning:
Estimator max depth: 6
Learning rate: 0.12
Estimator count: 150 XGBoost trees
Bag count: 25
Optimal threshold: 0.10
This configuration achieved a cross-validation balanced accuracy of approximately 68%, with outer GroupKFold (5 folds) providing a robust generalization estimate of 66.1% ± 3.9%.

Deep Learning Enhancement
Recognizing that the balanced accuracy could be improved, I developed a Deep Neural Network (DNN) model as an alternative approach. The DNN architecture was specifically designed to handle the class imbalance and high-dimensional feature space, achieving a significantly improved accuracy of 91.41% compared to the XGBoost with Balanced Bagging (65%).

Key Findings
Group-aware splitting is essential for preventing inflated metrics when samples share biological context
Ensemble methods excel at uncovering subtle signals in high-dimensional protein embeddings
Balanced Bagging with XGBoost remarkably outperformed single classifiers and standalone sampling methods, improving balanced accuracy by over 20% from baseline
Deep learning approaches can further improve performance on severely imbalanced biological datasets when properly tuned

Technologies Used
Python: Core programming language
Scikit-learn: Machine learning algorithms, preprocessing, and evaluation
XGBoost: Gradient boosting implementation
TensorFlow/Keras: Deep neural network implementation
Pandas & NumPy: Data manipulation and numerical operations
Matplotlib & Seaborn: Data visualization
Imbalanced-learn: Resampling techniques (SMOTE, ADASYN, etc.)

Business Impact
This work demonstrates practical approaches to real-world biomedical challenges where:
Class imbalance is severe and unavoidable
Domain knowledge must guide technical decisions (group-aware splitting)
Multiple modeling strategies should be compared systematically
Performance metrics must align with biological relevance (balanced accuracy over raw accuracy)
The methodologies developed here are applicable to similar problems in drug discovery, clinical diagnostics, and personalized medicine.

NB: The data CSV file is too huge to be uploaded here but it is a biological dataset of 45000 rows and 1650 features.
