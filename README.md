# Machine Learning-Based Intrusion Detection for Secure Defence Communication Networks

**Final Capstone Project 24.1 — Emeritus Machine Learning and Artificial Intelligence**  
**Author:** Sujit S Nair  
**Dataset:** CICIDS2017, Canadian Institute for Cybersecurity, University of New Brunswick

## Executive summary

Security teams need to distinguish ordinary network activity from traffic associated with cyberattacks. This project investigates whether machine-learning models can assist that decision using the publicly available CICIDS2017 dataset. The objective is to reduce missed attacks while keeping false alerts manageable, not to claim that a model is ready for live operational use.

**Main finding:** Among the four configurations compared on the same 20,000-record test set, the **original Random Forest achieved the highest attack-class F1-score (approximately 99.47%)**. A Decision Tree achieved the highest attack recall (approximately 99.64%). Grid-search tuning did not improve the original forest's test F1-score.

## Business problem

Traditional rule-based traffic monitoring can be difficult to maintain as network behavior and attack methods evolve. A machine-learning classifier may help analysts prioritize suspicious activity, but two mistakes matter: *missed attacks* (false negatives) and *false alarms* (false positives). The project evaluates both and presents the results in language suitable for technical and nontechnical readers.

## Data and approach

- **Source:** [Official CICIDS2017 dataset information](https://www.unb.ca/cic/datasets/ids-2017.html); eight machine-learning CSV files from the `MachineLearningCSV` download.
- **Initial data:** 2,830,743 labeled network-flow records and 79 columns (including the target label).
- **Quality checks:** 1,358 missing entries, 4,376 infinite numeric entries and 308,381 duplicate records.
- **Cleaning:** Infinite values were treated as missing; incomplete and duplicate rows were removed, leaving **2,520,798 records** (2,095,057 benign and 425,741 attack records).
- **Modeling sample:** A reproducible, stratified sample of 100,000 flows: 80,000 for training and 20,000 reserved for testing. **78 numeric predictors** were used, excluding the original and derived target columns.
- **Models:** Logistic Regression, Decision Tree and Random Forest, plus a GridSearchCV-selected Random Forest configuration.
- **Validation:** Three-fold stratified cross-validation of candidate models on the training data; GridSearchCV over eight parameter combinations using a separate 30,000-record subset of the training data.

The primary selection metric is **attack-class F1-score**, which balances precision (limiting false alarms) and recall (limiting missed attacks). Overall accuracy is reported but is insufficient alone because benign traffic is substantially more common than attack traffic.

## Results

These figures come from the executed final notebook's held-out **20,000-record test set**. Values are rounded.

| Model | Accuracy | Attack precision | Attack recall | Attack F1-score |
|---|---:|---:|---:|---:|
| Logistic Regression | 93.44% | 73.19% | 96.57% | 83.27% |
| Decision Tree | 99.58% | 97.91% | **99.64%** | 98.77% |
| **Random Forest** | **99.82%** | **99.58%** | 99.35% | **99.47%** |
| Tuned Random Forest | 99.82% | 99.58% | 99.32% | 99.45% |

The original Random Forest has the highest test F1-score in this comparison. Differences between the two forests are small and should not be treated as evidence of a meaningful real-world advantage without additional evaluation.

### Cross-validation and tuning

The three-fold training cross-validation F1-scores were approximately **84.83%** (Logistic Regression), **98.81%** (Decision Tree) and **99.32%** (Random Forest). The best GridSearchCV setting was 100 trees, maximum depth 15 and minimum samples per leaf 1, with mean CV F1 **98.9785%** on the smaller 30,000-row tuning subset. This score is **not directly comparable** with cross-validation performed on all 80,000 training rows.

### What the errors mean

For the tuned Random Forest, the test-set confusion matrix was:

| Actual class | Predicted benign | Predicted attack |
|---|---:|---:|
| Benign | 16,608 | 14 |
| Attack | 23 | 3,355 |

Thus, the tuned classifier correctly classified **19,963 of 20,000** network flows. It missed **23** attack records and raised **14** false alarms. These are outcomes on a historical evaluation sample, not expected live-system error rates.

## Key insights for stakeholders

1. Machine-learning classification appears promising **within the historical CICIDS2017 evaluation**.
2. Random Forest offered the strongest overall balance of attack precision and recall by F1-score in this experiment.
3. Decision Tree had marginally higher attack recall, warranting consideration if missed attacks carry greater operational costs.
4. Hyperparameter optimization is useful as a disciplined evaluation process, even when it does not improve final test performance.
5. Analysts should consider operational alert volumes, attack coverage, processing cost and robustness before selecting any system.

## Limitations and next steps

The random train/test split may place closely related network flows on both sides of the split and could overstate generalization. CICIDS2017 is historical and does not cover all current threats or live defence communications. The task is binary classification, so strong aggregate results may conceal weaker performance on rare attack types.

**Recommended next steps:** test on newer independent data; use time-separated or group-separated validation; analyze errors by attack category; assess model latency, stability and computational requirements; and only then explore supervised, human-in-the-loop monitoring pilots.

## Repository guide

- [Final technical notebook](notebooks/CICIDS2017_Final_Capstone_24_1.ipynb) — data preparation, exploratory analysis, plots, models, cross-validation, GridSearchCV, confusion matrix and conclusions.
- `requirements.txt` — project dependencies.
- `data/` — optional dataset notes only; the large underlying CSV files should **not** be committed to GitHub.

## How to reproduce

1. Download the eight `MachineLearningCVE` CSV files from the official CICIDS2017 distribution.
2. Place the data under the Google Drive path expected by the notebook, or update its `data_path` variable to the actual location.
3. Open the notebook in Google Colab, mount Drive and run cells **from top to bottom**. A high-memory runtime may be necessary.
4. Install any missing dependencies listed in `requirements.txt`.
5. Confirm each saved output against the current run. Results may differ if data processing, library versions or random partitions change.

**Reproducibility note:** the notebook uses random seed 42 for sampling and supported estimators. Raw data are not included because of their size. The notebook's saved cross-validation output is present, but its execution-count metadata should be refreshed during a clean full run.

## Dataset attribution

CICIDS2017, Canadian Institute for Cybersecurity, University of New Brunswick. See the [official dataset page](https://www.unb.ca/cic/datasets/ids-2017.html) for attribution and its associated research publication.
