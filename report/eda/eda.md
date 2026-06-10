### Class Imbalance 
The default rate was calculated as the proportion of records with loan_status = 1. The dataset exhibits class imbalance because the number of non-default loans significantly exceeds default loans. Class imbalance can lead to biased predictive models that favor the majority class and fail to detect defaults accurately. T
wo commonly used techniques to address this issue are SMOTE (Synthetic Minority Oversampling Technique) and Random Undersampling. Accuracy is not an appropriate metric for heavily imbalanced datasets; precision, recall, F1-score, and ROC-AUC should also be evaluated.

Two Techniques to Handle Class Imbalance
1. SMOTE - Synthetic Minority Oversampling Technique
`from imblearn.over_sampling import SMOTE`
Creates synthetic samples of the minority class.
2. Random Undersampling
`from imblearn.under_sampling import RandomUnderSampler`
Reduces the number of majority-class observations



###  KDE Curve

KDE Curve - A Kernel Density Estimation (KDE) curve is a smooth, continuous line used to represent the probability distribution of a dataset

Defaulted Mean: 660.11
Performing Mean: 680.68

Cohen's d measures how far apart the two distributions are.
Cohen's d = 0.243
| Cohen's d | Meaning    |
| --------- | ---------- |
| 0.2       | Small      |
| 0.5       | Medium     |
| 0.8       | Large      |
| >1.0      | Very large |

A common approximation:
Distribution Overlap = 90.31 %

| d   | Overlap |
| --- | ------- |
| 0.2 | 92%     |
| 0.5 | 80%     |
| 0.8 | 69%     |
| 1.0 | 62%     |
| 1.5 | 45%     |


Metric	Value
0	Mean CIBIL (Defaulted)	660.11
1	Mean CIBIL (Performing)	680.68
2	Cohen's d	0.24
3	Distribution Overlap (%)	90.31

### 12-panel histogram grid 
To produce a 12-panel histogram grid covering all key numeric features we choose variables that represent different aspects of lending.

[
 'loan_amnt_inr',
    'annual_inc_inr',
    'cibil_score',
    'dti_pct',
    'revol_bal_inr',
    'avg_cur_bal_inr',
    'total_rev_hi_lim_inr',
    'recoveries_inr',
    'expected_loss_inr',
    'cash_advance_inr',
    'cc_spend_last3m_inr',
    'avg_monthly_cc_spend_inr' ] 


A 12-panel histogram grid was produced to examine the distribution of key numeric variables. Several financial variables exhibited positive skewness. Features with skewness greater than 2.0 were identified and log-transformed using np.log1p() to reduce the influence of extreme values and improve suitability for regression modeling. The post-transformation skewness values were substantially lower, indicating more symmetric distributions and improved compliance with regression assumptions.