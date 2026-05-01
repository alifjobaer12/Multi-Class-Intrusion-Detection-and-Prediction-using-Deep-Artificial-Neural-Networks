# CICIDS2017 Multi-Class Intrusion Detection

Production-ready implementation of a multi-class network intrusion detection model trained on CICIDS2017 traffic data using a deep Artificial Neural Network (ANN) in TensorFlow/Keras.

## Overview

This repository contains the code, trained artifacts, and supporting documentation for a CICIDS2017-based intrusion detection model. The project focuses on classifying benign traffic and multiple attack categories from flow-based network features.

## Dataset Source

The dataset used in this project is the CICIDS2017 collection from Kaggle:

- Kaggle dataset: [CIC-IDS-2017](https://www.kaggle.com/datasets/chethuhn/network-intrusion-dataset)

Use this source if you need to download the original dataset or cite the dataset location in reports and presentations.

## Research Paper

This project is accompanied by a research paper documenting the problem statement, methodology, and results.

- Title: Multi-Class Intrusion Detection and Prediction using Deep Artificial Neural Networks
- Local file: [Multi-Class Intrusion Detection and Prediction using Deep Artificial Neural Networks.pdf](Multi-Class%20Intrusion%20Detection%20and%20Prediction%20using%20Deep%20Artificial%20Neural%20Networks.pdf)

Use this paper as the primary academic reference when citing this project in reports, presentations, or publications.

## Highlights

- Multi-class attack classification across 15 traffic classes.
- End-to-end training pipeline: load, clean, rebalance, scale, train, evaluate, and persist artifacts.
- Saved model, scaler, label encoder, confusion matrix, and training/ROC visualizations are already included.
- Notebook and Python script versions are both available.
- Clean project structure for local development and reproducible evaluation.

## Project Scope

This project trains a classifier to identify benign traffic and multiple attack categories from flow-based network features.

Current model uses:

- Framework: TensorFlow/Keras
- Input: CICIDS2017 CSV flow features
- Output: 15-class softmax prediction
- Balancing strategy: per-class target size of 5000 (undersampling + oversampling)

## Repository Structure

```text
.
|-- hacking_attack_prediction_model.py
|-- hacking_attack_prediction_model.ipynb
|-- dataset/
|   |-- Friday-WorkingHours-Afternoon-DDos.pcap_ISCX.csv
|   |-- Friday-WorkingHours-Afternoon-PortScan.pcap_ISCX.csv
|   |-- Friday-WorkingHours-Morning.pcap_ISCX.csv
|   |-- Monday-WorkingHours.pcap_ISCX.csv
|   |-- Thursday-WorkingHours-Afternoon-Infilteration.pcap_ISCX.csv
|   |-- Thursday-WorkingHours-Morning-WebAttacks.pcap_ISCX.csv
|   |-- Tuesday-WorkingHours.pcap_ISCX.csv
|   |-- Wednesday-workingHours.pcap_ISCX.csv
|-- artifacts/
|   |-- model/
|   |   |-- hacking_attack_prediction_model.h5
|   |   |-- scaler.pkl
|   |   |-- label_encoder.pkl
|   |-- reports/
|   |   |-- metrics.txt
|   |   |-- confusion_matrix.csv
|   |   |-- confusion_matrix.png
|   |   |-- roc_curve.png
|   |   |-- training_plot.png
|   |-- sample_dataset/
|   |   |-- random10_data.csv
|   |   |-- randow10_preprocess_data.csv
|   |   |-- randow10_preprocess_data_after_scaling.csv
|-- figures/
|   |-- system_flowchart.png
|   |-- three_layer_ANN_model.png
```

## Model Architecture

Sequential ANN with regularization and normalization:

1. Dense(128, ReLU) + BatchNormalization + Dropout(0.3)
2. Dense(64, ReLU) + BatchNormalization + Dropout(0.3)
3. Dense(32, ReLU) + BatchNormalization
4. Dense(15, Softmax)

Training configuration:

- Loss: sparse categorical cross-entropy
- Optimizer: Adam (learning rate = 1e-3)
- Metrics: accuracy
- Batch size: 64
- Max epochs: 1000
- Validation split: 0.2
- Callbacks:
  - EarlyStopping (patience=30, restore_best_weights=True)
  - ReduceLROnPlateau (factor=0.5, patience=5)

## Data Pipeline

1. Load and concatenate 8 CICIDS2017 CSV files.
2. Normalize column names using trim/strip.
3. Replace +/-infinity with NaN and drop NaN rows.
4. Save random inspection sample (`artifacts/sample_dataset/random10_data.csv`).
5. Balance each class to 5000 rows using mixed under/over-sampling.
6. Split train/test with stratification.
7. Standardize features with `StandardScaler`.
8. Encode labels with `LabelEncoder`.
9. Train model and evaluate predictions.
10. Persist model, preprocessing tools, and reports.

## Class Labels

The trained model predicts these 15 classes:

- BENIGN
- Bot
- DDoS
- DoS GoldenEye
- DoS Hulk
- DoS Slowhttptest
- DoS slowloris
- FTP-Patator
- Heartbleed
- Infiltration
- PortScan
- SSH-Patator
- Web Attack - Brute Force
- Web Attack - Sql Injection
- Web Attack - XSS

## Evaluation (Current Saved Run)

From `artifacts/reports/metrics.txt`:

- Accuracy: 0.9477333333333333
- Log Loss: 0.12045618352481818
- ROC AUC (OVR): 0.9967291142857142
- Matthews Correlation Coefficient: 0.9446928624015603

Generated visual reports:

- Training curves: `artifacts/reports/training_plot.png`
- Confusion matrix heatmap: `artifacts/reports/confusion_matrix.png`
- ROC curves: `artifacts/reports/roc_curve.png`

## Quick Start (Local Python)

### 1) Create and activate virtual environment

Windows (PowerShell):

```bash
python -m venv .venv
.venv\Scripts\Activate.ps1
```

Linux/macOS:

```bash
python -m venv .venv
source .venv/bin/activate
```

### 2) Install dependencies

```bash
pip install numpy pandas tensorflow scikit-learn matplotlib seaborn joblib
```

### 3) Adjust paths for local execution

The current script is exported from Google Colab and uses paths like:

- `/content/drive/MyDrive/AI_Project/dataset/...`
- `/content/drive/MyDrive/AI_Project/artifacts/...`

For local execution, replace these with repository-relative paths such as:

- `dataset/...`
- `artifacts/...`

Also remove or guard this section for local runs:

```python
from google.colab import drive
drive.mount('/content/drive')
```

### 4) Run training

```bash
python hacking_attack_prediction_model.py
```

## Google Colab Usage

If you prefer Colab:

1. Upload project to Google Drive.
2. Keep folder names aligned with `/content/drive/MyDrive/AI_Project/...`.
3. Open and run `hacking_attack_prediction_model.ipynb`.

## Inference Artifact Files

Saved under `artifacts/model/`:

- `hacking_attack_prediction_model.h5`: trained ANN model
- `scaler.pkl`: fitted `StandardScaler`
- `label_encoder.pkl`: fitted `LabelEncoder`

Typical inference sequence:

1. Load scaler and label encoder.
2. Transform incoming feature row with scaler.
3. Predict with model softmax.
4. Convert class index to label with label encoder.

## Known Gaps and Production Recommendations

Current repository is strong for research/demo. For stricter production maturity, consider:

- Add `requirements.txt` (or `pyproject.toml`) with pinned versions.
- Refactor script into modular package layout (`src/`, separate train/eval/infer modules).
- Add unit tests for preprocessing and inference consistency.
- Add config-driven paths (YAML/ENV) to remove hardcoded file locations.
- Add model versioning and experiment tracking (MLflow or similar).
- Add CI pipeline to validate linting, tests, and artifact schema.

## Citation

Includes supporting reference document:

- [Multi-Class Intrusion Detection and Prediction using Deep Artificial Neural Networks.pdf](Multi-Class%20Intrusion%20Detection%20and%20Prediction%20using%20Deep%20Artificial%20Neural%20Networks.pdf)

If you use this repository or model in academic work, cite both:

1. The repository
2. The included research paper

Suggested citation format:

```text
Author(s). "Multi-Class Intrusion Detection and Prediction using Deep Artificial Neural Networks."
Institution/Department, Year.
```

## Disclaimer

This project is for educational/research use on benchmark intrusion datasets. Validate behavior and robustness before deployment in real network defense systems.
