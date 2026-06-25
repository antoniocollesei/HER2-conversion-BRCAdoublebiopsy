# Development of a machine learning model to guide further metastatic biopsy decision in patients with HER2-0 status on both primary breast cancer and first biopsied metastatic site: who should undergo a repeated biopsy to detect HER2-low gain?

**Authors:** Federica Miglietta $^{1,2*}$, Antonio Collesei $^{3*}$, Claudio Vernieri $^{4,5}$, Tommaso Giarratano $^{2}$, Carlo Alberto Giorgi $^{2}$, Fabio Girardi $^{2}$, Gaia Griguolo $^{1,2}$, Matilde Cacciatore $^{6}$, Federico Piacentini $^{7}$, Andrea Botticelli $^{8}$, Andrea Vingiani $^{9,10}$, Lorenzo Casadei $^{7}$, Giuseppe Fotia $^{4,5}$, Davide Massa $^{1,2}$, Pietro Napolitano $^{1,2}$, Giusy Landa $^{1,2}$, Dario Palleschi $^{11}$, Giancarlo Pruneri $^{5,10}$, Rocco Cappellesso $^{12}$, Angelo Paolo Dei Tos $^{12,13}$, Maria Vittoria Dieci $^{1,2}$, Valentina Guarneri $^{1,2}$.

*\*FM and AC share the first authorship*

### Affiliations
1. Department of Surgery, Oncology and Gastroenterology (DiSCOG), University of Padua, Italy
2. Oncology 2, Istituto Oncologico Veneto IRCCS, Padova, Italy
3. Bioinformatics & Biostatistics, Clinical Research Unit, Istituto Oncologico Veneto, IOV – IRCCS
4. Department of Oncology and Hematology, Fondazione IRCCS Istituto Nazionale dei Tumori, Milan, Italy
5. Department of Oncology and Hematology, University of Milan, Milan, Italy
6. Department of Pathology and Molecular Genetics, Treviso General Hospital, Italy
7. Department of Medical and Surgical Sciences for Children and Adults, University Hospital of Modena, Italy
8. Sapienza University of Rome, Italy
9. Pathology and Laboratory Medicine, Fondazione IRCCS Istituto Nazionale dei Tumori, Milan, Italy
10. Department of Advanced Diagnostics, Fondazione IRCCS Istituto Nazionale dei Tumori, Milan, Italy
11. Medical Oncology Unit, Ca' Foncello Hospital, AULSS 2, Treviso, Italy
12. Institute of Anatomic Pathology, Padua University-Hospital, Padova, Italy
13. Department of Medicine (DIMED) University of Padua, Italy

**Corresponding Author:** Maria Vittoria Dieci (mariavittoria.dieci@unipd.it)  
Department of Surgery, Oncology and Gastroenterology, University of Padova; Division of Medical Oncology 2, Istituto Oncologico Veneto IRCCS. Via Gattamelata 64, 35128 Padova, Italy. Phone: +39 049 821 5295; Fax: +39 049 821 5932

---

## Workflow Steps

This project is organized into the following sequential steps, following the exact structure of the notebook, which is available in the `tutorial/` folder.

### 1. Data Loading & Initial Cleaning
* Loads the raw clinical dataset from an SPSS (`.sav`) file.
* Combines baseline HER2 features and creates the binary target variable (`Switch_HER2_status`).
* Configures clinical categorical variables and maps numerical variables.

### 2. Drop Future Data
* Drops all subsequent biopsy columns (`Biopsy2`) right away to prevent any data leakage before feature selection or modeling.

### 3. Apply Domain-Specific Cutoffs
* Converts continuous metrics (like ER/PgR percentages and time intervals) into binary flags using clinically relevant thresholds.

### 4. Define Features & Target
* Separates the dataset into predictors ($X$) and the target outcome ($y$).
* Formats categorical data types so they are ready for the imputation steps.

### 5. Train-Test Split
* Splits the dataset into a 60% training set and a 40% testing set.
* Uses stratified sampling to keep the 2:1 class imbalance consistent across both splits.
* Sets up an `IterativeImputer` and `StandardScaler` for numerical data, and a `SimpleImputer` and `OneHotEncoder` for categorical data.
* Applies L1-penalized Logistic Regression for automated feature selection.
* Configures an `XGBClassifier` with class weighting (`scale_pos_weight`) to balance the minority class.

### 7. Grid Search Cross-Validation
* Tunes the model using `GridSearchCV` with 5-fold `StratifiedKFold`.
* Optimizes hyperparameters using a custom scoring function that penalizes False Negatives more heavily than False Positives.

### 8. Evaluation
* Evaluates the optimized model on the completely unseen test set, printing the final classification report and best hyperparameter combinations.

### 9. SHAP Explainability on Test Set
* Uses SHAP (SHapley Additive exPlanations) beeswarm and bar plots to visualize global feature importance and explain what drives the model's predictions.

### 10. Confidence Interval
* Applies bootstrapping with 1,000 iterations to calculate the 95% Confidence Intervals for both precision and recall on the test set.

### 11. Decision Curve Analysis
* Runs Decision Curve Analysis (DCA) to determine the real-world clinical net benefit of using this model compared to default strategies.

---
## Requirements
To run this project, make sure you have the following packages installed:
```bash
pip install pandas numpy matplotlib scikit-learn xgboost shap pyreadstat dcurves
```