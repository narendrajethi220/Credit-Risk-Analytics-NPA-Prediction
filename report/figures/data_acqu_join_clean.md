### Memory Usage
| file                  |    rows |   cols |   mem_before_mb |   mem_after_mb |   reduction_pct |   parquet_mb |
|:----------------------|--------:|-------:|----------------:|---------------:|----------------:|-------------:|
| monthly_emi_track     | 2000000 |     23 |          847.43 |         288.02 |            66   |        78.58 |
| loan_enquiry_bureau   | 2000000 |     24 |         1163.11 |         192.02 |            83.5 |        42.88 |
| payment_history       | 2000000 |     18 |          392    |         314    |            19.9 |        76.44 |
| customer_bureau       | 2000000 |     30 |         1063.35 |         444.04 |            58.2 |       107.87 |
| credit_card_behavior  | 2000000 |     17 |          615.65 |         200    |            67.5 |        47.13 |
| branch_region_economy | 2000000 |     19 |          696.39 |         324    |            53.5 |        47.62 |
| loans_master          | 2000000 |     27 |         1640.74 |         250.02 |            84.8 |        67.29 |
| collateral_assets     | 2000000 |     20 |          885.53 |         214.02 |            75.8 |        32.17 |
| loan_performance      | 2000000 |     12 |          585.41 |         204    |            65.2 |        25.5  |

### loans_master: 2,000,000 rows × 27 columns
### CSV on disk ≈ 300MB (compressed text)
### Pandas in RAM:
###   - each float64 col  = 2M × 8 bytes = 16 MB
###   - each object col   = 2M × 50+ bytes = 100+ MB  ← this is the killer
###   - loans_master has ~10 object cols → 10 × 100MB = 1000MB+ just from strings!

2_000_000 * 8  / 1e6   # one float64 col  → 16.0 MB
2_000_000 * 50 / 1e6   # one object col   → 100.0 MB  (minimum!)
### WITH deep=True — counts actual Python object sizes recursively

Total Size of all  CSV file before downcasting in the Pandas - np.float64(7889.61) MB
Total Size of all CSV file after downcasting in the Pandas - np.float64(2430.12) MB 
Total Size of all CSV file after parquet - np.float64(525.48) MB

### Orphans Summary
An orphan record is:
A record whose key exists in the parent/master table but has no matching key in the related table being joined.

| table                 |   row_after_join |   orphans |
|:----------------------|-----------------:|----------:|
| customer_bureau       |          2000000 |         0 |
| loan_performance      |          2000000 |         0 |
| payment_history       |          2000000 |         0 |
| branch_region_economy |          2000000 |         0 |
| monthly_emi_track     |          2000000 |         0 |
| loan_enquiry_bureau   |          2000000 |         0 |
| credit_card_behavior  |          2000000 |         0 |
| collateral_assets     |          2000000 |         0 |

No Orphan record found!

 Data Quality Issue Columns

### Mostly percentage are 
1. gdp_growth_pct 139881
2. rate_spread_pct 23286
3. real_interest_rate_pct 76546
4. rejection_rate_pct 3168
5. ltv_ratio_pct 28700
#### Column:
1. rejection_rate_pct-Negative rejection_rates make no sense
2. ltv (loan to value) ratio - negative almost certainly invalid
Most houses are 1200-2500 sqft
3. property_area_sqft - 999 need to inspect,property_area_sqft=0 for 1679672 alomst 84% value ?
4. avg_monthly _cc_spend_inr - 999, 86 records/9999-55 records,pattern ? avg_monthly_cc_spend_inr = 0 ?
5. cash_advance_inr - 999
6. emit_to_income_ration = max=28.58 - No one pays 28 X income as EMI
7. AGE vs EMP_LENGTH CONSISTENCY
emp_length > (age - 18): 180929

8 data quality issues spend across 6 columns
| Issue No. | Column                   | Dirty Pattern               |
| --------- | ------------------------ | --------------------------- |
| 1         | rejection_rate_pct       | Negative values             |
| 2         | ltv_ratio_pct            | Negative values             |
| 3         | avg_monthly_cc_spend_inr | Value = 999                 |
| 4         | avg_monthly_cc_spend_inr | Value = 9999                |
| 5         | cash_advance_inr         | Value = 999                 |
| 6         | cash_advance_inr         | Value = 9999                |
| 7         | emp_length_years         | emp_length > age − 18       |
| 8         | emi_to_income_ratio      | Unrealistically high values |


| Column                   | Issue                      |
| ------------------------ | -------------------------- |
| rejection_rate_pct       | negative values            |
| ltv_ratio_pct            | negative values            |
| avg_monthly_cc_spend_inr | sentinel values 999 / 9999 |
| cash_advance_inr         | sentinel values 999 / 9999 |
| emp_length_years         | > age − 18                 |
| emi_to_income_ratio      | unrealistic extreme values |



Issue	Dirty_Record_Count
0	Negative rejection_rate_pct	0
1	Negative ltv_ratio_pct	0
2	avg_monthly_cc_spend_inr = 999	86
3	avg_monthly_cc_spend_inr = 9999	55
4	cash_advance_inr = 999	75
5	cash_advance_inr = 9999	5
6	emp_length_years > age-18	191754
7	emi_to_income_ratio > 5	557

based on the analysis you performed, 999 and 9999 in the spending columns were not conclusively proven dirty because they occurred with frequencies similar to neighboring values. I'm only including them here because you're following the "8 issues across 6 columns" interpretation for the assignment. From a strict EDA standpoint, the first 3-rule version is the most defensible.

# Missing Columns:

| Column                 | Type | Reason                                             |
| ---------------------- | ---- | -------------------------------------------------- |
| mths_since_last_delinq | MNAR | Missing often means no delinquency history         |
| il_util_pct            | MNAR | Missing often means no installment loans           |
| emp_length_years       | MAR  | Missing likely related to borrower characteristics |
| mort_acc               | MAR  | Missing related to housing/ownership profile       |

mths_since_last_delinq and il_util_pct were classified as MNAR because missing value themselves indicate meaningfull borrower conditions. 
Missingness indicators were created and values were imputed using sentinel values (-1 and 0 ).

mort_acc and emp_length_years were classified as MAR because missingness is likely related to other borrower characteristics rather than occurring completely at random. Median imputation was applied to these variables.

Missing values were verified using .isnull().sum() before and after imputation, confirming that all missing observations were successfully handled.