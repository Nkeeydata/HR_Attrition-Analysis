[README_HR.md](https://github.com/user-attachments/files/29502611/README_HR.md)
# HR Employee Attrition Prediction

## Project Overview

This project predicts whether an employee will **leave the company (attrition)** using HR records of 14,999 current and former employees, with the goal of helping HR teams identify at-risk employees before they resign. The dataset includes features such as satisfaction level, last performance evaluation score, number of projects, average monthly hours worked, tenure, work accidents, promotions, department, and salary band. A Random Forest Classifier substantially outperformed a Logistic Regression baseline, achieving **99.1% accuracy** and an **AUC of 0.991** on held-out test data, with satisfaction level, tenure, and number of projects emerging as the strongest predictors of attrition.

## Business Understanding

The stakeholder for this project is the company's HR and people-operations team, along with department managers and senior leadership concerned about retaining talent. Employee turnover is costly — replacing an employee typically costs a significant multiple of their salary in recruiting, onboarding, and lost productivity, and high attrition can also damage team morale and institutional knowledge. By identifying employees at elevated risk of leaving, HR can intervene proactively (e.g., adjusting workload, addressing satisfaction issues, or revisiting compensation/promotion timelines) rather than reacting after a resignation is already submitted. A reliable predictive model turns attrition from a surprise into a manageable, plannable risk.

## Data Understanding

The dataset (`HR_comma_sep.csv`) contains **14,999 employee records** with **10 columns**: the target variable `left` (1 = employee left, 0 = stayed), along with `satisfaction_level`, `last_evaluation`, `number_project`, `average_montly_hours`, `time_spend_company` (tenure in years), `Work_accident`, `promotion_last_5years`, `Department`, and `salary` (low/medium/high). The data is clean with **no missing values**. The dataset is also **imbalanced**, with about 76% of employees staying and 24% leaving, which is important to account for when evaluating model performance (accuracy alone can be misleading). No timeframe/date field is provided, so this should be treated as a static snapshot rather than a time series — it cannot capture how attrition risk evolves over time or responds to seasonal/organizational events. Other limitations include the lack of granular salary figures (only a 3-tier band) and no information on tenure-adjusted performance trends or external factors like job market conditions.

Exploratory analysis showed `satisfaction_level` has the strongest correlation with attrition (≈ -0.39, lower satisfaction → more likely to leave), followed by `time_spend_company` (≈ 0.14, longer-tenured employees somewhat more likely to leave) and `salary`/`Work_accident` (both negatively correlated, i.e., higher salary and accident history were associated with slightly lower attrition in this dataset).

## Modeling and Evaluation

Categorical variables were encoded (`Department` one-hot encoded, `salary` ordinally encoded as low/medium/high), and the data was split 80/20 into training and test sets, stratified by the target to preserve class balance. Two classifiers were trained and evaluated on the test set:

- **Logistic Regression**: Accuracy = 0.796, Precision = 0.623, Recall = 0.363, F1 = 0.458, AUC = 0.825
- **Random Forest Classifier** (300 trees): Accuracy = 0.991, Precision = 0.996, Recall = 0.966, F1 = 0.981, AUC = 0.991

Random Forest dramatically outperformed Logistic Regression across every metric, suggesting the relationship between features and attrition is non-linear (e.g., satisfaction and workload likely interact in threshold-like ways rather than a simple linear trend). Feature importance from the Random Forest model ranked `satisfaction_level`, `time_spend_company`, `number_project`, and `average_montly_hours` as the top drivers of attrition risk.

## Conclusion

The Random Forest model is highly effective at flagging employees likely to leave (AUC = 0.99), making it suitable for deployment as an early-warning tool for HR. I'd recommend using the model's predicted probability of leaving to prioritize outreach — for example, flagging employees with both low satisfaction scores and high project loads/hours for a check-in conversation or workload review. Given the strong performance, it's also worth validating the model isn't overfitting to this specific dataset/time period before deploying it; testing on a held-out future cohort would help confirm it generalizes. Future steps include monitoring model performance over time as new attrition data comes in, incorporating finer-grained compensation and engagement-survey data, exploring fairness/bias checks across departments and salary bands to ensure the model isn't unfairly targeting certain groups, and building a simple internal dashboard so HR can act on risk scores directly.
