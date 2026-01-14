# 💳 Cost-Optimized Credit Card Fraud Detection

**A business-first fraud detection system focused on minimizing financial loss, not maximizing vanity metrics.**

---

## 📝 The Problem: A Game of Unequal Mistakes

In credit card fraud detection, not all errors carry the same cost.

- **Missing a fraudulent transaction** results in direct financial loss and potential reputational damage.
- **Blocking a legitimate customer** introduces operational cost and churn risk.

Most machine learning projects optimize for technical metrics like **Accuracy**, which are misleading in highly imbalanced problems where fraud represents only **0.17%** of transactions.

**This project reframes fraud detection as a cost-minimization problem.**

---

## 💰 Decision Framework (Business Lens)

| Outcome | Business Impact | Assumed Cost* |
|------|---------------|---------------|
| **False Negative (Missed Fraud)** | Liability + chargeback | **$100** |
| **False Positive (Blocked Customer)** | Support cost + churn risk | **$5** |

**Objective:** Identify the operating point that minimizes **total expected financial loss**, not raw error count.

---

## 🚀 Solution Strategy

### 1️⃣ Leakage-Safe Model Design
A common mistake in fraud modeling is applying resampling techniques (e.g., SMOTE) **before** data splitting, which leads to data leakage. 

I implemented a strict **`imblearn` pipeline** to ensure:
- Scaling and resampling occur **only on training data**.
- The test set remains a realistic proxy for unseen, production-like data.

---

### 2️⃣ Using the Right Metric: PR-AUC
Given the extreme class imbalance:
- Accuracy and ROC-AUC provide an inflated sense of performance.
- I prioritized **Precision–Recall AUC (≈ 0.85)**, which directly measures fraud detection quality.

This confirms the model’s ability to separate fraudulent from legitimate transactions **before** applying business costs.


---

### 3️⃣ Threshold Optimization: The ROI Layer
Instead of defaulting to a 0.50 probability threshold, I performed a **threshold sweep**:

- Simulated **100 decision thresholds**.
- Computed total financial loss at each threshold using the $100/$5 cost assumptions.
- Identified the **cost-optimal operating threshold (0.69)**.

This step converts probability scores into **actionable business decisions**.


---

## 📊 Results (Evaluated on Test Set, Under Stated Assumptions)

| Strategy | Decision Rule | Relative Financial Impact |
|--------|---------------|--------------------------|
| **Naive Baseline** | Allow all transactions | **100% (Reference Loss)** |
| **Standard ML** | Default threshold (0.50) | **~10% of baseline loss** |
| **Optimized ML** | **Cost-optimized threshold (0.69)** | **~80% reduction vs baseline** |

**Key Insight:** Default thresholds are arbitrary. Explicitly pricing errors unlocks substantial financial improvement without retraining the model.

---

## 🕵️‍♂️ Explainability: Why Transactions Are Flagged

Fraud models cannot operate as black boxes in regulated environments. I integrated **SHAP** to provide:

- **Global explanations:** Identifying which behavioral components most influence fraud predictions.
- **Local explanations:** Visualizing why a specific transaction was flagged, enabling analyst review and override.


---

## 📂 Repository Structure

- `01_data_understanding.ipynb`  
  Sanity checks, fraud ratio validation, and feature constraints.

- `02_baseline_analysis.ipynb`  
  Establishes the “do nothing” financial cost floor.

- `03_model_pipeline.ipynb`  
  Leakage-safe ML pipeline with PR-AUC evaluation.

- `04_cost_optimization.ipynb`  
  Threshold sweep to minimize total expected financial loss.

- `05_explainability.ipynb`  
  SHAP-based global and local explanations for analyst review.

- `decision_framework.md`  
  Plain-English documentation of business assumptions and cost logic.

---

### ⚠️ Note on Assumptions
*The $100/$5 cost ratio is an educational assumption used to demonstrate the decision framework. In a production environment, these values would be derived from actual chargeback data, customer lifetime value, and operational costs.*
