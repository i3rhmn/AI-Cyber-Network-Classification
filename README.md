# AI Cyber Network Classification

## Overview

This project applies Artificial Intelligence and Machine Learning techniques to cybersecurity network traffic analysis.

The main objective is to build machine learning models capable of:

- Classifying network flows into different traffic and attack categories.
- Detecting unusual network flows using unsupervised anomaly detection.
- Identifying important network traffic features that influence model predictions.
- Comparing supervised learning models using appropriate evaluation metrics.
- Analysing challenges associated with minority attack classes and overlapping network traffic patterns.

The project uses a cyber network traffic dataset containing **21,185 network flows and 84 original attributes**. The target variable is the `Label` column, which contains **10 traffic classes**.

The supervised learning approaches implemented are:

- K-Nearest Neighbours (KNN)
- Linear Support Vector Machine (Linear SVM)

The unsupervised learning approach implemented is:

- Isolation Forest

The project also includes model interpretation using:

- Linear SVM coefficient-based feature importance
- Permutation importance
- Per-class feature analysis
- Anomaly feature-shift analysis

---

## Project Information

| Item | Details |
|---|---|
| Course | IT7009 – Artificial Intelligence |
| Assessment | Practical Project – Group |
| Institution | Bahrain Polytechnic |
| Project Area | Artificial Intelligence & Cybersecurity |
| Programming Language | Python |
| Development Environment | Google Colab / Jupyter Notebook |
| Main ML Library | Scikit-learn |
| Data Processing | Pandas, NumPy |
| Visualisation | Matplotlib |

---

## Project Objectives

The project was designed to address the following objectives:

- Load and inspect the cyber network traffic dataset.
- Validate data quality and integrity.
- Clean and format the dataset.
- Engineer useful features from timestamp information.
- Remove identifier fields that are not suitable as learning features.
- Create a numeric feature matrix for machine learning.
- Train multiple supervised classification models.
- Evaluate classification performance using multiple metrics.
- Tune the KNN hyperparameter `K`.
- Apply Isolation Forest for anomaly detection.
- Analyse the distribution of anomalies across traffic classes.
- Identify important features influencing the Linear SVM model.
- Compare anomalous and normal flows using feature median shifts.
- Save important model outputs as CSV files.
- Perform final quality-assurance checks.

---

# Dataset

The project uses the provided cyber network traffic CSV dataset.

Each row represents a **network flow**, which contains statistical information about network communication.

The dataset contains features related to:

- Packet lengths
- Inter-arrival times (IAT)
- TCP flags
- Flow duration
- Packet rates
- Byte rates
- Protocol
- Source and destination ports
- Other flow-level network statistics

### Dataset Dimensions

Initial dataset:

- **Rows:** 21,185
- **Columns:** 84
- **Target column:** `Label`
- **Number of classes:** 10

### Traffic Classes

| Class | Number of Samples |
|---|---:|
| Benign | 3,000 |
| Generic | 3,000 |
| DoS | 3,000 |
| Reconnaissance | 3,000 |
| Fuzzers | 3,000 |
| Exploits | 3,000 |
| Shellcode | 2,102 |
| Backdoor | 452 |
| Analysis | 385 |
| Worms | 246 |

Several major classes contain 3,000 samples, while classes such as Worms, Analysis, and Backdoor contain substantially fewer samples.

Because of this class imbalance, **macro-averaged precision, recall, and F1-score** were used so that minority classes are considered during evaluation.

---

# Project Workflow

The project follows an end-to-end machine learning workflow:

```text
Dataset
   |
   v
Data Loading & Inspection
   |
   v
Data Quality Audit
   |
   v
Data Cleaning
   |
   v
Feature Engineering
   |
   v
Feature Selection / Identifier Removal
   |
   v
Train / Test Split
   |
   +-----------------------+
   |                       |
   v                       v
Supervised Learning    Anomaly Detection
   |                       |
   +-- KNN                 +-- Isolation Forest
   |
   +-- Linear SVM
   |
   v
KNN Hyperparameter Tuning
   |
   v
Model Evaluation
   |
   v
Feature Importance & Interpretation
   |
   v
Final Analysis & Saved Outputs
1. Data Loading and Inspection

The dataset is loaded using Pandas.

The notebook checks:

Dataset dimensions
Column names
Data types
First rows of the dataset
Target column
Number of unique labels
Label distribution

The target variable used for classification is:

Label

The initial dataset contains:

21,185 rows
84 columns
10 classes
2. Data Quality Audit

Before training the machine learning models, the dataset was checked for common data-quality issues.

The following checks were performed:

Missing Values

The dataset was checked for missing values on a column-by-column basis.

Duplicate Rows

The dataset was checked for duplicate rows.

Object / Text Columns

Object columns were identified to determine which fields contained text or identifiers.

Infinite Values

Numeric columns were checked for positive and negative infinite values.

These checks help prevent unexpected problems during machine learning model training.

3. Data Cleaning

The following cleaning and formatting operations were performed:

Label Standardisation

Whitespace was removed from the Label values to ensure consistency.

Timestamp Conversion

The original Timestamp column was converted into a datetime format.

Numeric Conversion

Object columns expected to contain numeric values were converted into numeric data types.

Infinite Values

Positive and negative infinity values were handled during preprocessing.

Missing Numeric Values

Where necessary, numeric missing values were handled using the column median.

Duplicate Removal

Duplicate rows were removed before modelling.

The final data-quality checks confirmed:

No missing values
No duplicate rows
No infinite numeric values
4. Feature Engineering

Feature engineering was performed to improve the representation of the network traffic data.

Timestamp Features

Instead of simply removing the timestamp information, three numeric features were extracted:

ts_hour
ts_dayofweek
ts_month
ts_hour

Represents the hour of the day:

0 – 23
ts_dayofweek

Represents the day of the week:

0 = Monday
1 = Tuesday
2 = Wednesday
3 = Thursday
4 = Friday
5 = Saturday
6 = Sunday
ts_month

Represents the month:

1 – 12

After extracting these features, the original Timestamp column was removed.

5. Identifier Removal

The following columns were removed from the machine-learning feature matrix:

Label
Flow ID
Src IP
Dst IP

Label was removed because it is the target variable.

The following fields were treated as identifiers:

Flow ID
Src IP
Dst IP

These identifiers were removed because they can cause memorisation or data leakage instead of helping the model learn general network traffic patterns.

6. Numeric Feature Matrix

The remaining machine-learning features were converted into numeric form.

Constant columns were also removed.

A constant column contains only one unique value and therefore does not provide useful information for classification.

The project removed:

9 constant columns

The final feature matrix contained:

21,185 samples
73 numeric features

Therefore:

X shape = 21,185 × 73
7. Train/Test Split

The dataset was divided into training and testing sets using an 80/20 stratified split.

Training Set
16,948 flows
Testing Set
4,237 flows

Stratification was used to preserve the class distribution between the training and testing datasets.

8. Feature Scaling

Feature scaling was used because the models operate on numerical network traffic features that can have very different ranges.

StandardScaler was used inside Scikit-learn pipelines.

Scaling was particularly important for KNN because KNN is distance-based.

9. Supervised Machine Learning

Two supervised classification algorithms were implemented:

K-Nearest Neighbours
Linear Support Vector Machine
10. K-Nearest Neighbours

K-Nearest Neighbours (KNN) classifies a new network flow based on the labels of nearby training samples in feature space.

The baseline model used:

K = 5

KNN is relatively simple and non-parametric, but its performance can be affected by:

High-dimensional feature spaces
Irrelevant features
Feature scaling
Choice of K
Overlapping classes
11. Linear Support Vector Machine

A Linear Support Vector Machine (Linear SVM) was also implemented.

The model attempts to find separating hyperplanes between classes.

For multi-class classification, a one-vs-rest strategy is used.

Linear SVM can work effectively with high-dimensional numerical feature spaces.

12. Model Evaluation

The models were evaluated using:

Accuracy

Measures the overall percentage of correctly classified samples.

Macro Precision

Calculates precision independently for each class and then gives every class equal weight.

Macro Recall

Calculates recall independently for each class and then gives every class equal weight.

Macro F1

Calculates F1-score independently for each class and averages the results.

Macro-F1 was particularly important because the dataset contains minority classes.

13. Baseline Results

The initial supervised models produced the following results on the test set:

Model	Accuracy	Macro Precision	Macro Recall	Macro F1
KNN (k=5)	0.5702	0.5131	0.5120	0.5024
Linear SVM	0.6408	0.5739	0.5697	0.5430
14. KNN Hyperparameter Tuning

KNN was further tuned using 5-fold Stratified Cross-Validation.

The following K values were tested:

1
3
5
7
9
11
15

The optimisation metric was:

Macro-F1

The best cross-validation result was obtained using:

K = 9

with a cross-validation macro-F1 of approximately:

0.494
15. Tuned KNN Results

After tuning KNN:

Model	Accuracy	Macro Precision	Macro Recall	Macro F1
KNN (k=5)	0.5702	0.5131	0.5120	0.5024
KNN (k=9)	0.5733	0.5279	0.5092	0.5039
Linear SVM	0.6408	0.5739	0.5697	0.5430

The tuned KNN model showed a modest improvement compared with the baseline KNN model.

16. Confusion Matrix Analysis

Confusion matrices were generated for the supervised models.

The confusion matrices help identify which traffic classes are correctly classified and which classes are confused with one another.

The analysis showed that some attack categories have similar flow-level characteristics, making them difficult to distinguish.

Minority classes also presented additional challenges due to their smaller number of training examples.

17. Minority Class Challenges

The dataset contains several minority classes:

Worms
Analysis
Backdoor

These classes contain substantially fewer samples than the larger classes.

The Linear SVM results showed particularly weak performance for the Worms class, where the F1-score was near zero.

This demonstrates one of the challenges of applying machine learning to cybersecurity datasets:

A model can achieve reasonable overall performance while still struggling with specific low-frequency attack categories.

18. Unsupervised Anomaly Detection

Supervised classification depends on known labels.

To complement the supervised models, an unsupervised anomaly detection approach was implemented using:

Isolation Forest

Isolation Forest works by randomly partitioning the feature space.

Samples that are easier to isolate are considered more unusual.

19. Isolation Forest Configuration

The anomaly detection threshold was set to:

5%

The 5% threshold was used as a practical decision threshold for identifying the most unusual network flows.

20. Anomaly Detection Results

The Isolation Forest model flagged:

1,060 anomalous flows

This represents approximately:

5.00% of the dataset

The anomaly results were then analysed according to their known labels.

A large proportion of the flagged anomalies belonged to:

Generic
DoS
Exploits

Some flows labelled as Benign were also flagged as anomalies.

This is expected in unsupervised anomaly detection because a statistical anomaly does not necessarily mean that the traffic is malicious.

Therefore, anomaly detection should be considered a triage and investigation tool, rather than a definitive attack classifier.

21. Anomaly Feature Analysis

To understand what makes anomalous flows different from normal flows, feature median shifts were calculated.

The median values of anomalous flows were compared with the median values of non-anomalous flows.

The analysis showed strong differences in features related to:

Flow duration
Inter-arrival times
Packet characteristics
Traffic burst patterns
Flow byte rates

The most anomalous flows were associated with strong shifts in duration and inter-arrival-time features.

22. Model Interpretability

Understanding why a model makes predictions is important in cybersecurity.

Two feature-importance approaches were applied to the Linear SVM model:

Coefficient-based global feature importance
Permutation importance
23. Coefficient-Based Feature Importance

Linear SVM provides coefficients associated with its decision boundaries.

A global importance score was calculated using the mean absolute coefficient across classes.

Important features included:

Fwd PSH Flags
RST Flag Count
ts_month
FIN Flag Count
Down/Up Ratio
ts_dayofweek
Protocol
Fwd Seg Size Min
PSH Flag Count
SYN Flag Count

TCP flag-related features appeared prominently in the coefficient-based analysis.

24. Permutation Importance

Permutation importance was used as a model-agnostic method for validating feature usefulness.

The method randomly shuffles a feature and measures the resulting performance change.

The evaluation metric was:

Macro-F1

Important features included:

Bwd Packet Length Max
Fwd Seg Size Min
Bwd Packet Length Std
Packet Length Max
Packet Length Variance
Packet Length Std
Packet Length Mean
Dst Port

These results provide another perspective on which network traffic features contribute to predictive performance.

25. Per-Class Feature Drivers

The project also examined Linear SVM coefficients for individual traffic classes.

For each class, the analysis identified:

Top positive features
Top negative features

Positive coefficients represent features that push the prediction toward a particular class.

Negative coefficients represent features that push the prediction away from that class.

This provides more detailed insight than a single global feature-importance ranking.

26. Important Findings
TCP Flag Features

Features related to TCP flags such as:

PSH
RST
FIN
SYN

appeared as important predictors.

Different network behaviours can generate different TCP flag patterns.

Packet Length Features

Packet length statistics were also important.

These features can represent differences in traffic volume and packet behaviour.

Protocol and Destination Port

Protocol and destination-port features also contributed to the model.

These features can provide information about the network services involved in the traffic.

Time Features

Features such as:

ts_month
ts_dayofweek
ts_hour

appeared in feature rankings.

However, these features may capture characteristics specific to the dataset collection period and may not generalise to completely different traffic capture periods.

27. Practical Cybersecurity Application

The machine learning workflow demonstrates how AI can support security operations.

A supervised classifier can be used as a first-pass mechanism for assigning network flows to known categories.

For example:

Network Flow
     |
     v
Feature Extraction
     |
     v
Linear SVM
     |
     +-- Benign
     +-- Generic
     +-- DoS
     +-- Reconnaissance
     +-- Fuzzers
     +-- Exploits
     +-- Shellcode
     +-- Backdoor
     +-- Analysis
     +-- Worms

At the same time, Isolation Forest can identify statistically unusual traffic that may deserve additional analyst investigation.

A possible operational workflow is:

Network Traffic
      |
      v
Feature Extraction
      |
      +-------------------+
      |                   |
      v                   v
Supervised ML        Anomaly Detection
      |                   |
      v                   v
Known Class          Unusual Flow
      |                   |
      +---------+---------+
                |
                v
         Security Analyst
              Review
28. Limitations
Single Hold-Out Evaluation

The supervised models were evaluated using a single train/test split.

Repeated splits or nested cross-validation could provide stronger confidence in generalisation performance.

Minority Classes

Classes such as:

Worms
Analysis
Backdoor

contain relatively few samples.

This can reduce classification performance for those classes.

Similar Attack Signatures

Some attack types have overlapping flow-level characteristics.

This can make them difficult for machine learning models to distinguish.

Temporal Features

Timestamp-derived features may contain information specific to the dataset collection period.

This may reduce their usefulness when the model is applied to traffic collected during different periods.

Isolation Forest

Isolation Forest detects statistical outliers.

An anomaly is not automatically malicious.

For example, some flows labelled as Benign were also flagged as anomalies.

Therefore, anomaly detection should support analyst investigation rather than replace it.

29. Possible Future Improvements

Several improvements could be explored in future work:

SVM Hyperparameter Optimisation

Additional SVM hyperparameters, particularly C, could be tuned.

Class Weighting

Class weighting could be used to give more importance to minority classes.

Resampling

Oversampling or other resampling techniques could be investigated to improve minority-class performance.

Feature Selection

Feature selection could reduce noise and potentially improve model performance.

Dimensionality Reduction

Dimensionality-reduction techniques could be investigated, especially for distance-based models such as KNN.

Additional Models

Future experiments could include:

Decision Trees
Random Forest
Logistic Regression
Other SVM variants
Model Calibration

Probability calibration and classification thresholds could be investigated for operational deployment.

Explainable AI

Local explanation methods such as SHAP could provide explanations for individual network-flow predictions.

30. Repository Structure
AI-Cyber-Network-Classification/
|
+-- README.md
|
+-- code/
|   +-- it7009-ai-cyber-network-classification.ipynb
|
+-- code-output/
|   +-- supervised_model_comparison.csv
|   +-- svm_global_feature_importance.csv
|   +-- svm_per_class_report.csv
|   +-- svm_permutation_importance.csv
|   +-- knn_per_class_report.csv
|   +-- top200_anomalies.csv
|   +-- anomaly_label_distribution_pct.csv
|   +-- anomaly_rate_by_label.csv
|
+-- report/
    +-- it7009-ai-project-report.docx
31. Code Output Files

The code-output directory contains the CSV artefacts generated during the machine-learning workflow.

File	Description
supervised_model_comparison.csv	Comparison of supervised model performance
svm_global_feature_importance.csv	Global Linear SVM coefficient-based feature importance
svm_per_class_report.csv	Linear SVM classification results for individual classes
svm_permutation_importance.csv	Permutation importance results
knn_per_class_report.csv	KNN classification results for individual classes
top200_anomalies.csv	Highest-ranked anomalous network flows
anomaly_label_distribution_pct.csv	Distribution of labels among flagged anomalies
anomaly_rate_by_label.csv	Anomaly rate calculated for each traffic class
32. Technologies Used
Programming Language
Python
Data Processing
Pandas
NumPy
Machine Learning
Scikit-learn
Algorithms
K-Nearest Neighbours
Linear Support Vector Machine
Isolation Forest
Preprocessing
StandardScaler
Data cleaning
Numeric conversion
Median imputation
Timestamp feature extraction
Constant-feature removal
Model Evaluation
Accuracy
Macro Precision
Macro Recall
Macro F1-score
Classification Reports
Confusion Matrices
Cross-Validation
Permutation Importance
Visualisation
Matplotlib
Development Environment
Google Colab
Jupyter Notebook
33. Reproducibility

The main implementation is provided in the Jupyter Notebook:

code/it7009-ai-cyber-network-classification.ipynb

The notebook is organised into several stages:

Step A – Dataset Loading
Step B – Data Quality Audit and Cleaning
Step C – Feature Engineering
Step D – Supervised Learning
Step E – Anomaly Detection
Step F – Model Interpretation
Step G – Final Evaluation and QA

The experiments use fixed random states in the relevant procedures to improve reproducibility.

34. Running the Project
Requirements

Install the required Python libraries:

pip install pandas numpy scikit-learn matplotlib jupyter
Dataset

Place the cyber network traffic dataset in the working directory used by the notebook.

The notebook can then be opened using Jupyter Notebook or Google Colab.

Using Jupyter Notebook
jupyter notebook

Then open:

code/it7009-ai-cyber-network-classification.ipynb
Using Google Colab

Upload the notebook and required dataset to Google Colab, then run the notebook cells sequentially.

35. Main Results

The main reported supervised-learning results are:

KNN (k=5)

Accuracy:          0.5702
Macro Precision:   0.5131
Macro Recall:      0.5120
Macro F1:          0.5024
KNN (k=9)

Accuracy:          0.5733
Macro Precision:   0.5279
Macro Recall:      0.5092
Macro F1:          0.5039
Linear SVM

Accuracy:          0.6408
Macro Precision:   0.5739
Macro Recall:      0.5697
Macro F1:          0.5430

Isolation Forest:

Contamination:     5%
Flagged Flows:     1,060
Anomaly Rate:      5.00%
36. Final Reflection

This project demonstrates an end-to-end application of machine learning to cybersecurity network traffic.

The workflow covered:

Data Preparation
       |
       v
Feature Engineering
       |
       v
Supervised Classification
       |
       v
Hyperparameter Tuning
       |
       v
Anomaly Detection
       |
       v
Model Interpretation
       |
       v
Evaluation
       |
       v
Cybersecurity Analysis

The supervised experiments demonstrate the differences between KNN and Linear SVM when working with a relatively high-dimensional network-flow feature space.

The anomaly-detection component provides a complementary approach by identifying unusual traffic without relying exclusively on predefined labels.

The project also highlights important cybersecurity machine-learning challenges, including:

Class imbalance
Overlapping attack characteristics
High-dimensional feature spaces
Dataset-specific patterns
The difference between statistical anomalies and confirmed malicious activity
Authors

This project was completed as a group project for:

IT7009 – Artificial Intelligence

Bahrain Polytechnic

Group Members
Mohammed Alasfoor
Redha Mossa
Abdulelah Slais
Abdulrahman Altairey
Khalid Alzayed
Yousif Abdulsalam
Academic Context

This repository contains the implementation, generated outputs, and documentation associated with the IT7009 Artificial Intelligence practical project.

The project focuses on applying machine-learning concepts to a cybersecurity network-traffic classification and anomaly-detection problem.

The project demonstrates the application of supervised and unsupervised machine-learning techniques to support network security analysis and security-operations workflows.
