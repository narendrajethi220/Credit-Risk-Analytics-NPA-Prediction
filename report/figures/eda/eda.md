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

