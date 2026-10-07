# ShieldOps

**Network Traffic Classification and Cybersecurity Analytics**

ShieldOps is a cybersecurity analytics project focused on distinguishing malicious network traffic from benign traffic and identifying the network characteristics that contribute to those predictions.

The project was developed as a multi-stage analysis for DNSC 4211 and progressed from data-quality checks and exploratory analysis to a Random Forest classification model. The final modeling workflow uses six network-traffic features and a stratified 70/30 train-test split.

## Project Goals

- Explore network traffic for patterns associated with malicious activity
- Prepare network data for machine-learning analysis
- Classify traffic as benign or malicious
- Evaluate model performance using a confusion matrix and ROC curve
- Identify influential traffic features using Random Forest feature importance
- Translate model findings into useful cybersecurity and SOC insights

## Dataset

The project uses a publicly available cybersecurity threat-detection dataset containing **10,000 network-flow records across 13 features**. The dataset includes network traffic information, user-agent strings, protocols, port numbers, packet metadata, IP-related patterns and a labeled outcome indicating malicious or benign activity.

The original project identified several limitations:

- Approximately 4% of flows were labeled malicious, creating substantial class imbalance.
- The `url` field was missing from approximately one third of records.
- `bytes_sent` and `bytes_received` contained substantial skew and outliers.
- Several categorical fields required encoding.

The raw dataset is **not included in this repository**. See `data/README.md` for setup instructions and data-handling notes.

## Analysis Workflow

### 1. Data exploration

The original analysis inspected:

- Dataset shape and structure
- Descriptive statistics
- Missing values
- Duplicate records
- Class distribution

### 2. Data preparation

The final workflow:

- Converts `attack_type` into a binary target
- Treats `benign` as `0` and other attack types as `1`
- Converts timestamps and extracts the hour of day
- Calculates user-agent length
- Normalizes protocol labels
- Encodes supported protocols numerically
- Selects six modeling features
- Uses a stratified 70/30 train-test split

The six final model features are:

```text
bytes_sent
bytes_received
dst_port
protocol
hour
ua_length
```

### 3. Model

ShieldOps uses a **Random Forest classifier** with:

```text
n_estimators = 200
max_depth = None
class_weight = balanced
random_state = 32
```

Random Forest was selected because the project data contains nonlinear relationships, mixed feature types, outliers and imbalanced classes. It also provides feature-importance values that can be interpreted alongside the classification results.

## Results

The final project presentation reported:

- **ROC AUC: 0.732**
- **True negatives: 2,880**
- **False positives: 0**
- **False negatives: 113**
- **True positives: 7**

From that confusion matrix:

- Malicious recall = **5.8%**
- Malicious precision = **100%**
- Overall accuracy = **96.2%**

The accuracy should be interpreted carefully because the dataset is heavily imbalanced. The much more important finding is that the model missed **113 of 120 malicious test samples** at the default classification threshold. The ROC AUC of 0.732 indicates useful separation above random guessing, but the final classifier is not yet strong enough to serve as a standalone threat-detection system.

## Feature Importance

The final analysis identified the following features as particularly influential:

- `bytes_sent`
- `bytes_received`
- `dst_port`
- `ua_length`
- `hour`

The project analysis interpreted the traffic-volume features as important indicators of abnormal payload behavior. Destination ports also provided useful information because some attacks target particular services or ports. User-agent length and time-based behavior provided additional signals.

Feature importance indicates which variables were influential to the trained Random Forest. It does **not** by itself prove that a feature causes malicious behavior.

## Business / Security Value

The intended application is to support security analysts by helping prioritize network flows that deserve further investigation.

Potential uses include:

- Flagging suspicious flows for analyst review
- Reducing time spent manually reviewing large traffic volumes
- Highlighting traffic characteristics associated with suspicious activity
- Supporting future SOC alert-prioritization workflows

The current model should be treated as an analytical prototype rather than a production detection system.

## Repository Structure

```text
shieldops/
├── data/
│   └── README.md
├── notebooks/
│   └── README.md
├── results/
│   ├── README.md
│   └── figures/
├── src/
│   └── shieldops/
│       ├── __init__.py
│       ├── preprocessing.py
│       ├── model.py
│       └── analysis.py
├── tests/
│   └── test_pipeline.py
├── .gitignore
├── LICENSE
├── pyproject.toml
├── README.md
└── requirements.txt
```

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/samthosiac/shieldops.git
cd shieldops
```

### 2. Create a virtual environment

Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

macOS/Linux:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Add the dataset

Place the source dataset at:

```text
data/cybersecurity.csv
```

See `data/README.md` before adding data to the repository.

### 5. Run the analysis

```bash
python -m shieldops.analysis
```

Or:

```bash
python src/shieldops/analysis.py
```

## Development

Run the test suite with:

```bash
pytest
```

## Limitations and Future Work

The current project is a learning and research prototype. Important next steps include:

- Addressing the severe class imbalance more directly
- Comparing additional models
- Testing different probability thresholds
- Evaluating precision-recall performance
- Adding cross-validation
- Improving categorical feature encoding
- Investigating false negatives
- Adding automated data-validation checks
- Building a dashboard for analyst-facing review
- Evaluating the pipeline on additional datasets

## Project Evolution

ShieldOps was developed in stages:

1. **Data Check**: dataset quality, missing values, duplicates, class distribution and exploratory analysis
2. **Exploratory Analysis**: ports, protocols, packet sizes, traffic timing, user agents and correlations
3. **Statistical Learning**: feature engineering, Random Forest modeling and model evaluation
4. **Final Analysis**: interpretation, feature importance and business/security implications

The repository version consolidates these stages into a cleaner engineering structure while preserving the analytical decisions made in the original project.

## Author

**Sam-Haendell Thosiac**  
Computer Engineering & Business Analytics  
George Washington University
