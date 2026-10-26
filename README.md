# Predicting Diabetes Risk from Health Survey Data

Capstone project for the Post Graduate Program in AI and Machine Learning.
This repository contains the **initial report and exploratory data analysis (Module 20)**.

## Research question

Can lifestyle and health indicators from a national health survey predict whether a person has diabetes, and which factors matter most?

## Why this question is important

Diabetes often goes undetected for years, and complications such as heart disease, kidney damage and vision loss may start before it is found. A simple model built on easy survey questions could help clinics and public health teams decide who to screen first, and which habits to focus awareness campaigns on.

## Data

- **Source:** CDC Behavioral Risk Factor Surveillance System (BRFSS) 2015, in the cleaned Kaggle version *Diabetes Health Indicators Dataset* by Alex Teboul (license: CC0, public domain).
- **File used:** `data/diabetes_binary_health_indicators_BRFSS2015.csv`
- **Size:** 253,680 survey responses, 21 features and one target (`Diabetes_binary`: 0 = no diabetes, 1 = prediabetes or diabetes).
- **Features:** health indicators (high blood pressure, high cholesterol, BMI, stroke, heart disease), lifestyle (physical activity, smoking, fruit and vegetable intake, alcohol), access to care, self-rated general, mental and physical health, and demographics (age band, sex, education, income).

## Methods

1. **Data cleaning:** no missing values were found; 24,206 exact duplicate rows were removed (229,474 rows remain); value ranges were checked; outliers were analysed with the IQR rule. BMI was capped at the 99th percentile, and the skewed "unhealthy days" columns were kept because the values are real.
2. **Feature engineering:** capped BMI, BMI category, readable age groups, and a count of six health risk flags.
3. **Exploratory data analysis:** class balance, distributions of continuous variables, diabetes rates by health and lifestyle factors, age, general health, income and BMI category, and a correlation heatmap.
4. **Baseline model:** Logistic Regression with standardized features and balanced class weights, compared with a "always predict no diabetes" baseline and an unbalanced Logistic Regression. 80/20 stratified train/test split, plus 5-fold cross-validation.
5. **Evaluation metric:** **recall** for the diabetes class (main metric, because missing a person with diabetes is worse than a false alarm), with ROC-AUC, precision and F1 reported alongside. Accuracy is shown but not used to pick a model, because 85% of respondents do not have diabetes.

## Results

| Model | Accuracy | Recall | Precision | F1 | ROC-AUC |
|---|---|---|---|---|---|
| Always predict "no diabetes" | 0.847 | 0.000 | 0.000 | 0.000 | 0.500 |
| Logistic Regression (default) | 0.853 | 0.168 | 0.562 | 0.259 | 0.812 |
| **Logistic Regression (balanced), chosen baseline** | 0.715 | **0.762** | 0.319 | 0.450 | 0.812 |

- The balanced baseline finds about **76% of people with diabetes**, at the cost of many false alarms (about 68% of the people it flags do not have diabetes).
- 5-fold cross-validation gives recall 0.755 (std 0.005) and ROC-AUC 0.809 (std 0.003), so the result is stable.
- Accuracy would have suggested that the "always no diabetes" model is good, even though it finds nobody. This is why recall and ROC-AUC are used.

### Key findings from the exploratory analysis

- The diabetes rate rises steadily with **age** (about 1% in the youngest group to about 23% at ages 70 to 74).
- Self-rated **general health** is the strongest signal: about 3% with excellent health versus about 38% with poor health.
- **High blood pressure, high cholesterol, heart disease, stroke and difficulty walking** each go with a rate two to four times higher than for people without that condition.
- **Obese** respondents have a rate of about 24%, versus about 6% to 7% for underweight and normal weight.
- **Lower income** goes with a higher rate; **physical activity** goes with a lower rate.
- The largest coefficients in the baseline model are general health, BMI, age and high blood pressure.

## Limitations

- Self-reported survey data from 2015, with possible reporting errors; may not describe the population today.
- The target combines prediabetes and diabetes and cannot separate type 1 from type 2.
- Some removed duplicate rows may have been real, different people with identical answers.
- The results show association, not causation, and the model is a screening aid, not a diagnosis.

## Next steps (Module 24)

- Compare the baseline with Decision Tree, Random Forest and Gradient Boosting models, tuned with cross-validation.
- Choose the decision threshold using the trade-off between recall and precision.
- Check model performance across age and income groups.
- Write the non-technical final report with recommendations on who to screen first.

## Repository structure

```
diabetes-risk-capstone/
├── README.md
├── data/
│   └── diabetes_binary_health_indicators_BRFSS2015.csv
├── notebooks/
│   └── 01_initial_report_eda.ipynb
└── reports/
    └── Initial_Report_Summary.docx
```

## How to run

1. Install Python 3.10+ and the packages: `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, `jupyter`.
2. Open `notebooks/01_initial_report_eda.ipynb` in Jupyter and choose **Run All**. The notebook reads the data from `../data/`.
