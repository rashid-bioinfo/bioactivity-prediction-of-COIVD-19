<p align="center">
  <h1 align="center">COVID-19 Bioactivity Prediction</h1>
  <p align="center">
    <b>ML-QSAR Framework for pIC50 Prediction of Replicase Polyprotein Inhibitors</b><br>
    <i>Cheminformatics · QSAR modelling · Random Forest · Streamlit · PubChem fingerprints · ChEMBL</i>
  </p>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-3776AB?style=flat-square&logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat-square&logo=jupyter&logoColor=white" alt="Jupyter">
  <img src="https://img.shields.io/badge/Streamlit-App-FF4B4B?style=flat-square&logo=streamlit&logoColor=white" alt="Streamlit">
  <img src="https://img.shields.io/badge/scikit--learn-ML-F7931E?style=flat-square&logo=scikit-learn&logoColor=white" alt="scikit-learn">
  <img src="https://img.shields.io/badge/RDKit-Cheminformatics-blue?style=flat-square" alt="RDKit">
  <img src="https://img.shields.io/badge/ChEMBL-Data%20Source-red?style=flat-square" alt="ChEMBL">
  <img src="https://img.shields.io/badge/PaDEL-Descriptor-green?style=flat-square" alt="PaDEL">
  <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=flat-square" alt="License">
</p>

---

## Abstract

This repository presents an end-to-end machine learning QSAR pipeline for predicting the bioactivity (pIC50) of small molecules against the **SARS-CoV-2 Replicase Polyprotein** — a validated drug target for COVID-19 therapeutics. The framework retrieves IC50 bioactivity data directly from ChEMBL, performs exploratory chemical space analysis with Lipinski descriptor profiling, computes 881-bit PubChem fingerprints via PaDEL-Descriptor, and trains a Random Forest regression model validated against a held-out 20% test set. Model performance is benchmarked against 40+ scikit-learn regression algorithms using LazyPredict. The trained model is deployed as an interactive **Streamlit web application** that accepts user-supplied SMILES input and returns predicted pIC50 values with a downloadable CSV output.

---

## Table of Contents

- [Key Features](#key-features)
- [Target Biology](#target-biology)
- [Pipeline Overview](#pipeline-overview)
- [Repository Structure](#repository-structure)
- [Data Description](#data-description)
- [Bioactivity Classification Thresholds](#bioactivity-classification-thresholds)
- [Descriptors and Feature Engineering](#descriptors-and-feature-engineering)
- [Machine Learning Model](#machine-learning-model)
- [Streamlit Web Application](#streamlit-web-application)
- [Output Files](#output-files)
- [Installation and Dependencies](#installation-and-dependencies)
- [Usage](#usage)
- [Author](#author)

---

## Key Features

| Feature | Description |
|---|---|
| **COVID-19-specific target** | Trained on IC50 data for SARS-CoV-2 Replicase Polyprotein from ChEMBL |
| **Automated ChEMBL query** | IC50 records retrieved via REST API using `chembl_webresource_client` |
| **Activity classification** | Active / inactive / intermediate labels based on IC50 thresholds |
| **Lipinski EDA** | MW, LogP, HBD, HBA computed via RDKit with box plots and scatter plots |
| **PubChem fingerprints** | 881-bit binary fingerprints via PaDEL-Descriptor |
| **Random Forest QSAR model** | pIC50 regression with 80/20 train/test split, R² and MSE evaluation |
| **LazyPredict benchmarking** | Automated comparison of 40+ regression algorithms |
| **Streamlit web app** | Interactive browser-based tool for predicting pIC50 of novel compounds |
| **One-click CSV download** | Predictions exportable directly from the web app |
| **Serialised model** | Trained model saved as `COVID19_model.pkl` for reuse |

---

## Target Biology

| Property | Detail |
|---|---|
| **Target name** | Replicase polyprotein 1ab (nsp1–nsp16) |
| **Organism** | *SARS-CoV-2* (COVID-19) |
| **Target class** | Viral non-structural polyprotein |
| **Relevance** | The replicase polyprotein is essential for SARS-CoV-2 genome replication and transcription; it is proteolytically cleaved into 16 non-structural proteins (nsps), several of which (e.g. nsp5 / Mpro, nsp12 / RdRp) are primary antiviral drug targets |
| **Bioactivity type** | IC50 (half-maximal inhibitory concentration, nM) |
| **ChEMBL query** | `target.search('coronavirus')` → `target_chembl_id[6]` |

---

## Pipeline Overview

### Step 1 — Data Collection

Query ChEMBL for SARS-CoV-2 Replicase Polyprotein IC50 records, handle missing values, and classify compounds into active / inactive / intermediate classes.

![Data Collection](https://user-images.githubusercontent.com/11457522/171391180-086cd63c-118d-408f-8650-943759d92395.png)

---

### Step 2 — Exploratory Data Analysis

Compute Lipinski descriptors via RDKit, convert IC50 to pIC50, and visualise the chemical space distribution of active vs inactive compounds.

![Exploratory Data Analysis](https://user-images.githubusercontent.com/11457522/171391374-5bb35993-c3b3-44c7-8f89-8bbe060215a2.png)

---

### Step 3 — Descriptor Calculation

Export SMILES to `molecule.smi` and compute 881-bit PubChem fingerprints using PaDEL-Descriptor via `padel.sh`.

![Descriptors Calculation](https://user-images.githubusercontent.com/11457522/171391446-2427e32e-e163-4fa2-9c39-2c2f803f8b39.png)

---

### Step 4 — Model Building

Apply VarianceThreshold feature selection, split data 80/20, train a Random Forest Regressor, and evaluate on the held-out test set.

![Model Building](https://user-images.githubusercontent.com/11457522/171391489-c0eac0ba-2daf-479b-862d-7897128c44c5.png)

---

### Step 5 — Model Comparison

Benchmark 40+ scikit-learn regression algorithms automatically using LazyPredict on the same 80/20 split.

---

### Step 6 — Model Deployment

Train the final Random Forest (n_estimators=500) on the full dataset, export `COVID19_model.pkl`, and deploy via the Streamlit web application.

![Model Deployment](https://user-images.githubusercontent.com/11457522/171391540-f68e5c44-befe-4703-b1a6-2e29d6188dae.png)

---

### Full pipeline summary

```
ChEMBL → IC50 data → Activity classification
       → Lipinski EDA → pIC50 conversion
       → PubChem fingerprints (PaDEL)
       → VarianceThreshold feature selection
       → Random Forest QSAR model
       → LazyPredict benchmarking
       → COVID19_model.pkl
       → Streamlit web app (interactive pIC50 prediction)
```

---

## Repository Structure

```
bioactivity-prediction-of-COIVD-19/
│
├── 1-Data-Collection-COVID19.ipynb                          # ChEMBL query and activity labelling
├── 2-Exploratory-Data-Analysis-COVID19.ipynb                # Lipinski EDA, pIC50, visualisations
├── 3-Descriptor-Calculation-COVID19.ipynb                   # PaDEL PubChem fingerprints
├── 4-Model_Building_COVID19.ipynb                           # Random Forest training and evaluation
├── 5-Model-Comparison-COVID19.ipynb                         # LazyPredict multi-algorithm benchmark
├── 6-Model-deployment-COVID19.ipynb                         # Final model and pickle export
│
├── padel.sh                                                 # Shell script to run PaDEL-Descriptor
├── PaDEL-Descriptor/                                        # PaDEL-Descriptor JAR and XML config
│
├── bioactivity-prediction-app-main/                         # Streamlit web application
│   ├── app.py                                               # Main Streamlit application
│   ├── COVID19_model.pkl                                    # Deployed trained model
│   ├── descriptor_list.csv                                  # Selected feature names for prediction
│   ├── descriptors_output.csv                               # PaDEL output (generated at runtime)
│   ├── PaDEL-Descriptor/                                    # PaDEL JAR for app-side calculation
│   ├── logo.png                                             # App logo
│   ├── compounds.txt                                        # Example input file
│   ├── requirements.txt                                     # App dependencies
│   └── bioactivity_prediction_app.ipynb                     # App development notebook
│
├── 1_bioactivity_data_Data_Collection_part.csv              # Raw ChEMBL IC50 records
├── 2_bioactivity_data_PROCESSED_Data_Collection_part.csv    # Cleaned + activity class labels
├── 3_COVID19_bioactivity_data_3class_pIC50_*.csv            # + Lipinski descriptors + pIC50
├── 4_COVID19_bioactivity_data_3class_pIC50_pubchem_fp_*.cvs # PubChem fingerprints + pIC50
│
├── molecule.smi                                             # SMILES input for PaDEL
├── descriptors_output.csv                                   # Full 881-bit fingerprint matrix
├── descriptor_list.csv                                      # Variance-filtered features (deployment)
├── COVID19_model.pkl                                        # Serialised trained model
│
├── EDA_plot_bioactivity_class.pdf                           # Bioactivity class frequency plot
├── EDA_plot_MS_vs_LogP.pdf                                  # MW vs LogP scatter plot
├── EDA_BP_plot_pIC50.pdf                                    # pIC50 box plot (active vs inactive)
└── results.zip                                              # Compressed results archive
```

---

## Data Description

| Parameter | Value |
|---|---|
| **Target** | SARS-CoV-2 Replicase Polyprotein |
| **ChEMBL query** | `target.search('coronavirus')` → index 6 |
| **Activity type** | IC50 (nM) |
| **Data source** | ChEMBL database |
| **Preprocessing** | Rows with missing `standard_value` removed; NaN `molecule_chembl_id` dropped |
| **Columns retained** | `molecule_chembl_id`, `canonical_smiles`, `standard_value`, `bioactivity_class` |

---

## Bioactivity Classification Thresholds

| Class | IC50 threshold | Interpretation |
|---|---|---|
| **Active** | ≤ 1,000 nM | Potent inhibitors |
| **Intermediate** | 1,000 – 10,000 nM | Moderate activity; excluded from binary model training |
| **Inactive** | ≥ 10,000 nM | Non-inhibitors |

IC50 values are capped at **100,000,000 nM** (100 μM) before conversion to prevent extreme pIC50 values.

**IC50 → pIC50 conversion:**

```
pIC50 = −log₁₀(IC50 in M)
```

---

## Descriptors and Feature Engineering

### Lipinski Descriptors (RDKit) — EDA only

| Descriptor | Definition |
|---|---|
| `MW` | Molecular weight (Da) |
| `LogP` | Octanol–water partition coefficient |
| `NumHDonors` | Number of hydrogen bond donors |
| `NumHAcceptors` | Number of hydrogen bond acceptors |

### PubChem Fingerprints (PaDEL-Descriptor) — Model features

- **Type:** PubChem fingerprints (`PubchemFingerprinter.xml`)
- **Dimensionality:** 881 binary bits per compound
- **Preprocessing:** Salt removal, nitro group standardisation
- **Tool:** PaDEL-Descriptor (Java ≥ JRE 1.8)

```bash
# padel.sh — executed by Notebook 3 and the Streamlit app
java -Xms1G -Xmx1G -Djava.awt.headless=true \
  -jar ./PaDEL-Descriptor/PaDEL-Descriptor.jar \
  -removesalt -standardizenitro -fingerprints \
  -descriptortypes ./PaDEL-Descriptor/PubchemFingerprinter.xml \
  -dir ./ -file descriptors_output.csv
```

### Feature Selection

`sklearn.feature_selection.VarianceThreshold` is applied to remove near-constant features:

| Context | Threshold | Notes |
|---|---|---|
| Notebooks 4 & 5 (exploration) | 0.16 (≈ 80/20 split) | Model building and LazyPredict comparison |
| Notebook 6 (deployment) | 0.10 | Final model; selected columns saved to `descriptor_list.csv` |

---

## Machine Learning Model

### Algorithm

**Random Forest Regressor** (`sklearn.ensemble.RandomForestRegressor`)

| Parameter | Notebook 4 (exploration) | Notebook 6 (deployment) |
|---|---|---|
| `n_estimators` | 100 | 500 |
| `random_state` | 100 | 42 |
| Training set | 80% | Full dataset |
| Test set | 20% | Full dataset (training evaluation) |
| Evaluation | R² | R², MSE |

### LazyPredict Model Comparison (Notebook 5)

`LazyRegressor` automatically fits and evaluates all available scikit-learn regression algorithms on the same 80/20 split, reporting **R²**, **RMSE**, and **computation time** for each. Results are visualised as horizontal bar charts.

### Model file

```python
import pickle
model = pickle.load(open('COVID19_model.pkl', 'rb'))
predictions = model.predict(X_new)  # X_new must match descriptor_list.csv columns
```

---

## Streamlit Web Application

The `bioactivity-prediction-app-main/` directory contains a fully deployable Streamlit application for interactive compound prediction.

### Application workflow

```
User uploads .txt file (SMILES + compound names)
         │
         ▼
PaDEL-Descriptor calculates PubChem fingerprints on-the-fly
         │
         ▼
Descriptor subset aligned to descriptor_list.csv
         │
         ▼
COVID19_model.pkl predicts pIC50 for each compound
         │
         ▼
Prediction table displayed in browser
         │
         ▼
CSV download link generated
```

### Input file format

The app accepts a space-delimited `.txt` file with two columns — SMILES and compound name:

```
CC(=O)Oc1ccccc1C(=O)O  Aspirin
c1ccc2ccccc2c1  Naphthalene
```

### Running the app locally

```bash
cd bioactivity-prediction-app-main

# Install dependencies
pip install -r requirements.txt

# Launch the app
streamlit run app.py
```

The app will open at `http://localhost:8501` in your browser.

### App dependencies

```
streamlit==0.71.0
pandas==1.1.3
Pillow
pickle
subprocess
```

> **Note:** Java (JRE ≥ 1.8) must be installed and accessible for PaDEL descriptor calculation within the app.

---

## Output Files

| File | Description |
|---|---|
| `1_bioactivity_data_Data_Collection_part.csv` | Raw ChEMBL IC50 records |
| `2_bioactivity_data_PROCESSED_Data_Collection_part.csv` | Cleaned data with activity class labels |
| `3_COVID19_bioactivity_data_3class_pIC50_*.csv` | + Lipinski descriptors + pIC50 (3 classes) |
| `4_COVID19_bioactivity_data_3class_pIC50_pubchem_fp_*.cvs` | PubChem fingerprints (881-bit) + pIC50 |
| `descriptors_output.csv` | Full PaDEL fingerprint matrix |
| `descriptor_list.csv` | Variance-filtered feature names for deployment |
| `molecule.smi` | SMILES file used as PaDEL input |
| `COVID19_model.pkl` | Serialised Random Forest model (pickle) |
| `EDA_plot_bioactivity_class.pdf` | Bioactivity class frequency bar chart |
| `EDA_plot_MS_vs_LogP.pdf` | MW vs LogP scatter plot coloured by class |
| `EDA_BP_plot_pIC50.pdf` | pIC50 box plot: active vs inactive |
| `results.zip` | Compressed archive of all CSV and PDF outputs |

---

## Installation and Dependencies

### Python packages

```bash
pip install chembl_webresource_client rdkit pandas numpy scikit-learn \
            seaborn matplotlib lazypredict streamlit Pillow
```

| Package | Purpose |
|---|---|
| `chembl_webresource_client` | ChEMBL database query |
| `rdkit` | Lipinski descriptors, SMILES processing |
| `pandas` / `numpy` | Data manipulation |
| `scikit-learn` | Random Forest, VarianceThreshold, metrics |
| `seaborn` / `matplotlib` | Visualisation |
| `lazypredict` | Multi-algorithm benchmarking (Notebook 5) |
| `streamlit` | Web application deployment |
| `Pillow` | Logo image handling in the app |

### PaDEL-Descriptor

Requires **Java Runtime Environment (JRE) ≥ 1.8**.

```bash
# Ubuntu / Debian
sudo apt install -y default-jre

# macOS (Homebrew)
brew install --cask temurin

# Verify
java -version
```

The `PaDEL-Descriptor/` directory containing the JAR file and fingerprint XML must be present in the working directory when running `padel.sh` or the Streamlit app.

---

## Usage

### Running the notebooks

Execute notebooks sequentially:

```bash
jupyter notebook
```

| Step | Notebook | Action |
|---|---|---|
| 1 | `1-Data-Collection-COVID19.ipynb` | Query ChEMBL, retrieve IC50, classify compounds |
| 2 | `2-Exploratory-Data-Analysis-COVID19.ipynb` | Lipinski EDA, pIC50 conversion, visualisations |
| 3 | `3-Descriptor-Calculation-COVID19.ipynb` | Run PaDEL fingerprints via `padel.sh` |
| 4 | `4-Model_Building_COVID19.ipynb` | Train and evaluate Random Forest (80/20 split) |
| 5 | `5-Model-Comparison-COVID19.ipynb` | Benchmark all sklearn regressors with LazyPredict |
| 6 | `6-Model-deployment-COVID19.ipynb` | Train final model, export `COVID19_model.pkl` |

### Predicting on new compounds (Streamlit app)

1. Prepare a `.txt` file with your query SMILES (see input format above).
2. Launch the app:
   ```bash
   cd bioactivity-prediction-app-main
   streamlit run app.py
   ```
3. Upload your `.txt` file in the sidebar and click **Predict**.
4. Download the predictions as CSV.

### Predicting programmatically

```python
import pickle
import pandas as pd

# Load model and feature list
model = pickle.load(open('COVID19_model.pkl', 'rb'))
desc_list = pd.read_csv('descriptor_list.csv')

# Load pre-computed PaDEL fingerprints for query compounds
query_fp = pd.read_csv('descriptors_output.csv').iloc[:, 1:]  # drop Name column
query_fp = query_fp[desc_list.columns]  # align to training features

# Predict pIC50
predictions = model.predict(query_fp)
print(predictions)
```

---

## Author

**Rashid Hussain**, PhD, RSci, MRSC  
Postdoctoral Researcher in Computational Pathology  
Humanitas Research Hospital (IRCCS), Milan, Italy  
[rashid.bioinfo@gmail.com](mailto:rashid.bioinfo@gmail.com) · [https://rashid-bioinfo.github.io](https://rashid-bioinfo.github.io)

---

<p align="center">
  <i>QSAR · Cheminformatics · Machine Learning for Antiviral Drug Discovery</i>
</p>
