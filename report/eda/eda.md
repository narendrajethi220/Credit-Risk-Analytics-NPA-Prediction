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



| Feature 1             | Feature 2              | Why                              |
| --------------------- | ---------------------- | -------------------------------- |
| loan_amnt_inr         | funded_amnt_inr        | Funded amount ≈ loan amount      |
| sanctioned_amount_inr | disbursed_amount_inr   | Very similar concepts            |
| total_cc_limit_inr    | total_cc_balance_inr   | Related credit measures          |
| installment_inr       | annual_installment_inr | One derived from the other       |
| provision_inr         | expected_loss_inr      | Provision based on expected loss |



### Why is High Correlation a Problem?
This is called Multicollinearity
When multiple feature comtain almost the same information.

#### Problem 1:
Unstable Coefficients.
OLS struggles to determine: Is default explained by one feature or other  feature.
Coefficients become unstable.

#### Problem 2:
High correlation increases coefficient uncertainity.

loan_amnt coefficient = +5.2

next run

loan_amnt coefficient = -3.8

even though data barely changed.

#### Problem 3: Difficult Interpretation
We cannot reliable interpret the effect of individual predictors.

A Pearson correlation matrix was computed for the top 20 numeric features and visualized using an annotated heatmap. Several highly correlated predictor pairs were identified with absolute correlation exceeding 0.75, including:

| Feature 1             | Feature 2              | Correlation (r) | Interpretation                                                             |
| --------------------- | ---------------------- | --------------: | -------------------------------------------------------------------------- |
| total_pymnt_inr       | total_pymnt_inv_inr    |           1.000 | Almost identical payment measures                                          |
| sanctioned_amount_inr | disbursed_amount_inr   |           0.999 | Disbursed amount is based on sanctioned amount                             |
| loan_amnt_inr         | funded_amnt_inr        |           0.998 | Funded amount closely matches loan amount                                  |
| collateral_value_inr  | prop_value_inr         |           0.972 | Property value largely determines collateral value                         |
| total_emi_due_inr     | total_emi_paid_inr     |           0.987 | Higher dues generally imply higher repayments                              |
| total_rec_prncp_inr   | loan_amnt_inr          |           0.988 | Principal recovered depends on original loan size                          |
| total_pymnt_inr       | total_rec_prncp_inr    |           0.983 | Principal recovery forms a major part of payments                          |
| total_cc_limit_inr    | total_cc_balance_inr   |           0.835 | Customers with higher limits tend to have higher balances                  |
| loan_amnt_inr         | annual_installment_inr |           0.838 | Larger loans generate larger annual installments                           |
| total_rec_prncp_inr   | total_rec_int_inr      |           0.767 | Higher principal repayments generally generate higher interest collections |


What would be removed before OLS?
A reasonable choice would be:
| Keep                  | Drop                 |
| --------------------- | -------------------- |
| loan_amnt_inr         | funded_amnt_inr      |
| sanctioned_amount_inr | disbursed_amount_inr |
| total_pymnt_inr       | total_pymnt_inv_inr  |
| prop_value_inr        | collateral_value_inr |
