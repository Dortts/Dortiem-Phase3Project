# SyriaTel Customer Churn Prediction

## Overview
This project builds a binary classification model to predict whether a SyriaTel customer will churn. Using a dataset of 3,333 customers and 20 features, we trained and compared three models — Logistic Regression, Decision Tree, and Random Forest — to identify the best approach for proactively flagging at-risk customers.

---

## Business and Data Understanding

### Stakeholder
The **Customer Retention Team** at SyriaTel, a telecommunications company.

### Business Problem
Customer churn is one of the most expensive problems in telecom. Acquiring a new customer costs approximately 5× more than retaining an existing one. The retention team needs a reliable way to identify customers likely to churn before they leave, so they can intervene with targeted offers and outreach.

### Dataset
- **Source**: [SyriaTel Customer Churn Dataset (Kaggle)](https://www.kaggle.com/datasets/becksddf/churn-in-telecoms-dataset)
- **Size**: 3,333 rows × 20 features
- **Target variable**: `churn` (True/False)
- **Class balance**: ~14.5% churn rate (imbalanced)
- **Key features**: total day minutes, customer service calls, international plan, voice mail plan, total day charge

Identifier columns (`phone number`, `area code`, `state`) were removed as they are not predictive of churn.

---

## Primary Result

Most customers did not churn:
![alt text](<Target Variable Distribution.png>)

---

## Modeling

Three models were built iteratively:

1. **Logistic Regression (Baseline)** — A simple, interpretable linear model used to establish a performance floor. Requires scaled data.

2. **Decision Tree (Nonparametric)** — A nonparametric model that makes no assumptions about feature distributions. Can capture nonlinear patterns such as customers who call support 4+ times churning at much higher rates.

3. **Random Forest (Ensemble)** — An ensemble of decision trees using bagging and random feature subsets to reduce overfitting. Explored as a more powerful alternative to a single tree.

All models used `class_weight='balanced'` to handle the imbalanced target. Data was split 80/20 before scaling to prevent data leakage. `StandardScaler` was fit on training data only.

---

## Evaluation

**Primary metric: Recall** — Missing a churner (false negative) is more costly than a false alarm (false positive), so recall was prioritized.

| Model | Train Recall | Test Recall | Test ROC-AUC | Overfit Gap |
|---|---|---|---|---|
| Logistic Regression | 0.764 | **0.742** | 0.815 | **0.022** |
| Decision Tree | 0.855 | 0.722 | 0.803 | 0.133 |
| Random Forest | 0.909 | 0.701 | 0.888 | 0.208 |

**Selected model: Logistic Regression**

Despite being the simplest model, Logistic Regression achieved the highest test recall (74.2%) with the lowest overfit gap (0.022), meaning it generalizes reliably to new, unseen customers. Random Forest had the best ROC-AUC but a large overfit gap (0.208), making it unreliable for real-world deployment without further tuning.

Comparison based on ROC/AUC curve: ![alt text](<Images/ROC comparison.png>)

---

## Conclusion

The Logistic Regression model is recommended for deployment. It identifies approximately 74% of churners before they leave, giving the retention team time to intervene.

**Top churn drivers:**
1. **Customer service calls** — 4+ calls is a strong churn signal
2. **Total day charges** — High usage customers feel price pressure most acutely
3. **International plan** — Subscribers churn at higher rates than average
4. **No voicemail plan** — Lower engagement correlates with higher churn

**Recommendations:**
- Score all customers monthly and flag anyone with churn probability above 40%
- Intervene after the 2nd customer service call — not the 4th
- Proactively offer plan reviews to high day-charge customers
- Investigate the international plan experience through customer surveys

If the model catches 74% of churners and retention efforts save 30% of them, SyriaTel could reduce monthly churn by approximately 22%.
