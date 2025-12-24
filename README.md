# Starbucks Uplift Modeling Project

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

## 📊 Project Overview

This project implements an **Uplift Modeling** solution using the Starbucks promotional dataset to identify which customers are most likely to respond positively to marketing campaigns. The goal is to optimize marketing spend by targeting only customers who would convert *because of* the campaign, not those who would convert anyway.

### 🎯 Business Impact

**Key Results:**
- **97.7%** of customers identified as "Sure Things" (will convert without targeting)
- **2.29%** identified as "Persuadables" (converts only with campaign)
- **$48,853/month** theoretical savings in ad spend efficiency (at 200K monthly users, $0.25 CPC)
- **AUUC Score: 0.0131** - Model successfully isolates incremental lift

---

## 🏗️ Architecture

### Modeling Approach: T-Learner

```
┌─────────────────────────────────────────────────────────┐
│                   T-Learner Architecture                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Control Model (μ₀)     Treatment Model (μ₁)          │
│         ↓                        ↓                     │
│  GradientBoost            GradientBoost                │
│         ↓                        ↓                     │
│    P(Y|T=0, X)              P(Y|T=1, X)               │
│         └────────────┬───────────┘                     │
│                      ↓                                 │
│              Uplift = μ₁(X) - μ₀(X)                   │
└─────────────────────────────────────────────────────────┘
```

### 4-Quadrant Customer Segmentation

| Segment | Definition | Treatment Strategy |
|---------|------------|-------------------|
| **Persuadables** (2.29%) | High uplift, low baseline | ✅ **TARGET** - Only converts with campaign |
| **Sure Things** (97.7%) | High baseline conversion | ❌ Skip - Converts without campaign |
| **Lost Causes** | Low uplift, low baseline | ❌ Skip - Won't convert regardless |
| **Sleeping Dogs** | Negative uplift | ❌ **NEVER TARGET** - Campaign hurts conversion |

---

## 📁 Project Structure

```
uplift_project/
├── notebooks/
│   ├── uplift_analysis.ipynb    # Main analysis (all steps)
│   └── data/
│       └── demo_uplift.csv      # Sample output data
├── requirements.txt             # Python dependencies
└── README.md                   # This file
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.8+
- Jupyter Notebook
- Starbucks dataset (portfolio.json, profile.json, transcript.json)

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd uplift_project

# Create virtual environment
python -m venv .venv
.venv\Scripts\activate  # Windows
# source .venv/bin/activate  # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook
```

### Running the Analysis

1. Open `notebooks/uplift_analysis.ipynb`
2. Update data directory path in Step 2:
   ```python
   data_dir = r"C:\path\to\your\Starbucks dataset"
   ```
3. Run all cells (Kernel → Restart & Run All)

---

## 📊 Features (26 Total)

### Demographic Features
- Age, income, membership tenure
- Gender encoding (F, M, O)
- Age groups (middle, senior, elderly)
- Income quartiles (low, medium, high, very_high)

### Behavioral Features
- Total spend, average transaction, transaction count
- Spending standard deviation, transaction frequency
- Derived features: age×income interaction, spend per membership day

### Segmentation Flags
- is_active, is_high_value, is_frequent

---

## 🔬 Methodology

### Step-by-Step Pipeline

1. **Data Loading & Cleaning** - Starbucks promotional dataset (17K customers, 306K events)
2. **Feature Engineering** - Create 26 predictive features from transaction history
3. **Treatment Definition** - BOGO offer recipients vs. control group
4. **T-Learner Training** - Separate GradientBoosting models for treatment/control
5. **Uplift Scoring** - Calculate individual-level uplift predictions
6. **4-Quadrant Segmentation** - Classify customers into actionable groups
7. **Business Impact Analysis** - Calculate ROI and cost savings
8. **Validation** - Predicted vs. actual uplift correlation
9. **Production Monitoring** - KS tests for data drift detection

### Key Technical Decisions

**Why T-Learner over other methods?**
- Simple, interpretable, and production-ready
- Separate models capture heterogeneous treatment effects
- No special libraries required (pure scikit-learn)

**Outcome Definition:**
- "Made at least 1 purchase during observation period"
- Ensures both treatment and control groups have class variation
- Critical for model convergence

---

## 📈 Results

### Model Performance

| Metric | Value |
|--------|-------|
| AUUC | 0.0131 |
| Mean Uplift | 0.0012 |
| Persuadables | 2.29% |
| Sure Things | 97.7% |

### Business Impact (Simulated)

**Assumptions:**
- 200,000 monthly targeted users
- $0.25 cost per impression (CPC)
- $50 average customer value

**Results:**
- Users to exclude: 195,400 (97.7%)
- Monthly savings: **$48,853**
- Annual savings: **$586,236**

### Feature Distribution Monitoring

**Drift Detection (KS Test):**
- 16/19 features stable ✅
- 3/19 features show drift (x13, x14, x16) ⚠️
- Model performance: Healthy (AUUC = 0.0131)

---

## 🛠️ Technologies Used

- **Python 3.x** - Core programming language
- **pandas** - Data manipulation
- **scikit-learn** - Machine learning (GradientBoostingClassifier)
- **matplotlib/seaborn** - Visualization
- **scipy** - Statistical testing (KS test)
- **numpy** - Numerical computing
- **Jupyter Notebook** - Interactive development

### Optional Libraries
- **CausalML** - Alternative uplift models (UpliftRandomForest)
- **SHAP** - Model explainability
- **MLflow** - Model versioning and tracking

---

## 📚 Production Recommendations

### 1. Validation
- Run holdout A/B test with randomized control
- Track actual conversions over 30-90 days
- Compare predicted vs. actual uplift

### 2. Monitoring
- **Daily:** Conversion rates, uplift distribution
- **Weekly:** KS tests for feature drift
- **Monthly:** Model retraining and performance review

### 3. Deployment
- Save models to cloud storage (S3/Azure Blob)
- Serve predictions via REST API (Flask/FastAPI)
- Implement CI/CD with automated retraining
- Use MLflow for model versioning

### 4. Alert Thresholds
- KS test p-value < 0.05 → Retrain alert
- AUUC drops > 20% → Performance alert
- Conversion rate changes > 30% → Business alert

---

## 📖 References

- [Causal Inference Book](https://www.hsph.harvard.edu/miguel-hernan/causal-inference-book/)
- [Uplift Modeling Tutorial](https://www.uplift-modeling.com/)
- [scikit-learn Documentation](https://scikit-learn.org/)

---

## 📝 License

This project is licensed under the MIT License.

---

## 👤 Author

**Emine**
- GitHub: [@yourusername](https://github.com/yourusername)
- LinkedIn: [Your Profile](https://linkedin.com/in/yourprofile)

---

## 🙏 Acknowledgments

- Starbucks for providing the promotional dataset
- Udacity Data Science Nanodegree program
- CausalML open-source contributors

---

## 🔮 Future Enhancements

- [ ] Implement Meta-Learner (X-learner, S-learner)
- [ ] Add SHAP explanations for model interpretability
- [ ] Deploy as REST API with Docker
- [ ] Create interactive dashboard with Plotly Dash
- [ ] Implement multi-armed bandit for dynamic targeting
- [ ] Add propensity score matching for better causal inference

---

## 📫 Contact

- **GitHub:** [@emineerdogane](https://github.com/emineerdogane)
- **LinkedIn:** [Emine Erdogan](https://www.linkedin.com/in/emine-erdogan/)

---

**⭐ If you found this project helpful, please consider giving it a star!**
