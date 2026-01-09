Regression ML — Predicting Oral Temperature from Facial Infrared Thermography

PROJECT OVERVIEW
----------------
Infrared thermography (IRT) is widely used for non-contact screening of elevated body temperature (EBT), especially during infectious disease outbreaks. In practice, IRT-based fever screening can be inconsistent because accuracy depends on device quality and deployment conditions (ambient temperature, distance, humidity, ROI choice, calibration, etc.).

The Problem
-----------
Oral thermometers (especially in monitor mode) are more reliable than quick non-contact screening, but they are slower and less scalable. This creates a practical need for models that translate facial thermal measurements + context into a better estimate of true oral temperature.

The Solution
------------
This project builds regression models that predict oral temperature (monitor mode) using:
- facial temperature features extracted from infrared images (multiple facial ROIs), and
- contextual variables (environment + demographics).

ABOUT THE DATA
--------------
Dataset Summary: PhysioNet “Facial and oral temperature data from a large set of human subject volunteers” (v1.0.0)
This project uses the PhysioNet dataset released by Wang et al. (2023), which contains de-identified measurements from a clinical thermographic imaging study of more than 1000 subjects. For each subject, the dataset includes facial temperature variables extracted from infrared thermographs and paired oral thermometer readings.

Key points:
- Two evaluated infrared thermographs (IRTs) were used:
  - FLIR (IRT-1)
  - ICI  (IRT-2)
- Data are split into two ambient-temperature groups:
  - Group 1: ambient temperature 20.0–24.0 °C
  - Group 2: ambient temperature 24.0–29.0 °C
- After exclusions (incomplete records, inconsistent oral readings, motion blur), the dataset provides:
  - 1020 subjects for FLIR (IRT-1)
  - 1009 subjects for ICI  (IRT-2)
- The release includes six CSV files: FLIR_group1, FLIR_group2, FLIR_groups1and2, and the analogous three for ICI.

Why this Dataset?
-----------------
Compared to many public fever-screening datasets, this resource is notable for its scale and controlled acquisition. The authors emphasize rigor in the data collection process and note it was designed to evaluate best-practice deployment conditions.

What this project uses (subset + target)
----------------------------------------
This project is treated as a regression task:
- Target variable: aveOralM (average oral temperature, monitor mode).
- Inputs: facial thermal features + contextual variables.
- Subset choice (typical for this project):
  - FLIR_groups1and2
  - Round 1 features (to reflect a realistic “single screening” setting rather than repeated measurements).

Feature groups (high level)
---------------------------
PhysioNet provides:
- 26 facial temperature variables derived from defined facial regions (see Table 1 in the dataset documentation/files).
- Additional variables including oral temps (fast + monitor mode), demographics (gender, age group, ethnicity),
  environmental conditions (ambient temperature, humidity), distance, cosmetic use, and timestamp fields (see Table 2).

MODELING APPROACH (REGRESSION)
------------------------------
Goal
----
Predict oral temperature (monitor mode) from facial IRT-derived features + context.

Typical preprocessing
---------------------
A standard supervised ML pipeline for tabular regression is used:
- train/test split (hold-out test set),
- imputation for missing values (some subjects have incomplete context),
- one-hot encoding for categorical predictors (e.g., Age group, Ethnicity),
- scaling where appropriate (especially for neural nets).

Models evaluated (example set)
------------------------------
- Linear Regression (baseline),
- Random Forest Regressor,
- XGBoost Regressor,
- MLP Regressor (neural network).

Metrics
-------
- MAE (Mean Absolute Error)
- RMSE (Root Mean Squared Error)
- R² (Coefficient of Determination)

HOW TO GET THE DATA
-------------------
Option A — Download from PhysioNet (recommended)
You can download all dataset files via ZIP download, wget, or AWS S3 sync.

Example:
    wget -r -N -c -np https://physionet.org/files/face-oral-temp-data/1.0.0/

Option B — Use only one CSV
If you only want one file (e.g., FLIR_groups1and2.csv), download it directly from the “Files” section on the dataset page.

RUNNING THE PROJECT (TEMPLATE)
------------------------------
1) Environment
--------------
    python -m venv .venv
    source .venv/bin/activate  # macOS/Linux
    # .venv\Scripts\activate   # Windows
    pip install -U pip
    pip install numpy pandas scikit-learn matplotlib xgboost

2) Put the data in place
------------------------
Example:
    data/
      FLIR_groups1and2.csv

3) Run the notebook / script
----------------------------
- If you use a notebook: open and run your regression notebook (e.g., Regression ML.ipynb).
- If you use scripts: run your train.py / evaluate.py.

NOTES ON REPRODUCIBILITY
------------------------
- Because there are repeated measurement rounds in the raw dataset, decide early whether you treat this as:
  - a single-round prediction task (more realistic screening), or
  - a multi-round task (better accuracy, less realistic operationally).
- Some variables (e.g., timestamps) may not be useful predictors and can be excluded depending on your research question.
  The provided workflow commonly drops SubjectID, date/time fields, and the fast-mode oral temperature when defining the target.

LICENSE & CITATION
------------------
Dataset license
---------------
The PhysioNet dataset is released under Creative Commons Zero (CC0 1.0).

How to cite the dataset
-----------------------
Wang, Q., Zhou, Y., Ghassemi, P., et al. (2023). Facial and oral temperature data from a large set of human subject volunteers (version 1.0.0). PhysioNet. DOI: 10.13026/3bhc-9065

REFERENCES
----------
See the PhysioNet dataset page for background, methods, and references to associated clinical study papers and standards.
