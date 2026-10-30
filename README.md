# Predicting Diabetes Risk from Health Survey Data

Capstone project for the Post Graduate Program in AI and Machine Learning.
This report is written for readers **without a background in ML or AI**. The technical work is in the two Jupyter notebooks linked below.

## Summary

- **Question:** Can a few easy health and lifestyle questions tell us who is most likely to have diabetes, so that clinics can decide who to test first?
- **Answer:** Partly. A model built on a national survey can pick out a group that holds most people with diabetes. Testing the **30% of people with the highest risk score finds about 70% of all cases**, while picking 30% at random would find only 30%.
- **Catch 1:** It is a *screening aid*, not a diagnosis. When set to find about 8 in 10 people with diabetes, only about 3 in 10 of the people it flags really have diabetes. The rest need a follow-up test that shows they are fine.
- **Catch 2:** It works **much worse for young adults** (it finds fewer than 1 in 4 of the cases aged 18 to 34) and somewhat worse for higher-income people. This must be fixed or handled before any real use.

## Why this question matters

Diabetes often goes unnoticed for years, and by the time it is found, damage to the heart, kidneys and eyes may already have started. Testing everyone is expensive. If a short list of questions can show who is more likely to have diabetes, clinics and public health teams can spend their limited testing budget where it is most likely to help, and focus awareness campaigns on the right habits and groups.

## The data

- **Source:** the US CDC's 2015 *Behavioral Risk Factor Surveillance System* health survey, in the cleaned Kaggle version *Diabetes Health Indicators Dataset* (public domain license).
- **Size:** 253,680 people answered 21 questions. After removing 24,206 exact duplicate rows, **229,474 responses** were used.
- **Questions include:** blood pressure, cholesterol, weight (BMI), smoking, exercise, diet, self-rated general health, age group, income and education.
- **Outcome:** whether the person has been told they have diabetes or prediabetes. About **15%** of respondents say yes.

## What was done (in plain words)

1. **Cleaned the data:** checked for missing or impossible values, removed duplicates, and limited a few extreme weight values so they would not distort results.
2. **Looked at the data** with charts to see which answers go together with diabetes (notebook 1).
3. **Built a simple first model** (notebook 1), as a yardstick.
4. **Tried three more advanced models** (a decision tree, a random forest and gradient boosting) and compared them with the simple one (notebook 2). Each was tested several times on different slices of the data, and its settings were tuned by trying many combinations.
5. **Chose how cautious the model should be.** The model gives each person a risk score. We decided that a person is "flagged" when the score is high enough to find about **80% of people with diabetes**, because missing someone with diabetes is worse than sending a healthy person for an extra test.
6. **Checked fairness:** looked at how well the model works for different age groups, income groups and for women and men.

The final test used 45,895 people that the model had **never seen** while it was being built or tuned.

## Results

### Which model was best?

| Model | How well it ranks people by risk (0.5 = guessing, 1.0 = perfect) | Share of flagged people who really have diabetes, when 80% of cases are found |
|---|---|---|
| Simple first model (Logistic Regression) | 0.812 | 30.5% |
| Decision Tree | 0.804 | 28.9% |
| Random Forest | 0.816 | 30.8% |
| **Gradient Boosting (chosen)** | **0.819** | **31.5%** |

**All four models perform similarly.** The most advanced model is only slightly better than the simple one. The survey answers contain a limited amount of information, and a more complex model cannot create information that is not there.

### How the chosen model performs on the test group

| | Result |
|---|---|
| People with diabetes found | **5,704 of 7,019 (81%)** |
| People with diabetes missed | 1,315 (19%) |
| Healthy people flagged for a follow-up test | 12,704 of 38,876 (33%) |
| Flagged people who really have diabetes | 31% (about 3 in 10) |

### Who to test first

If a clinic can only follow up the people with the highest risk scores:

| Test the top ... of people | Share of all diabetes cases found | Random selection would find |
|---|---|---|
| 10% | 33% | 10% |
| 20% | 54% | 20% |
| 30% | 70% | 30% |
| 40% | 81% | 40% |
| 50% | 89% | 50% |

## Important findings

### What goes with diabetes
- **Self-rated general health** matters most. About 3% of people who rate their health as excellent have diabetes, against about 38% of those who rate it poor.
- **Weight (BMI)** and **age** come next. The rate rises steadily with age, from about 1% in the youngest group to about 23% at ages 70 to 74, and is about 24% among obese people against 6% to 7% among people of normal weight.
- **High blood pressure** and **high cholesterol** also matter. The rate is two to four times higher in people with these, or with heart disease, stroke or difficulty walking.
- **Lower income** goes with a higher rate, and **physical activity** goes with a lower rate.

These are links, not proof of cause. For example, the model does not show that high blood pressure *causes* diabetes.

### Where the model works less well

| Group | Share who have diabetes | Share of their cases the model finds |
|---|---|---|
| Age 18-34 | 2% | **23%** |
| Age 35-49 | 8% | 60% |
| Age 50-64 | 17% | 81% |
| Age 65+ | 22% | 87% |
| Income under $20k | 25% | 90% |
| Income $20-35k | 19% | 86% |
| Income $35-75k | 14% | 78% |
| Income $75k+ | 10% | 70% |
| Women | 14% | 80% |
| Men | 17% | 82% |

- **Young adults are mostly missed.** Age is one of the strongest signals, so younger people get a low risk score even when they have diabetes. With one cut-off for everybody, most young adults with diabetes would not be flagged. The model does rank younger people sensibly among themselves, so a separate, lower cut-off for younger groups is likely to help (at the cost of more false alarms there).
- **Higher-income people are also missed more often** (70% found against 90% for the lowest income group).
- Women and men get similar results.

## Limitations

- The data are **self-reported answers from 2015**. People may remember or report things wrongly, and the population may have changed since.
- "Diabetes" here includes prediabetes, and type 1 and type 2 cannot be separated.
- The model sees only survey answers, not blood tests, so it cannot be very precise.
- Some removed duplicate rows may have been real people with identical answers.
- Only a small set of model settings was tried, to keep the work practical.
- Some groups (such as young adults with diabetes) have few cases, so their numbers are less certain.

## Recommendations and next steps

1. **Use the model only as a screening aid** to decide who to test first. Anyone flagged still needs a proper diabetes test, and anyone with symptoms should be tested whatever the score.
2. **Do not use one cut-off for everyone.** Try separate cut-offs for adults under 50, and agree with health professionals on how many false alarms each group can bear.
3. **Test on newer data** (for example the 2022 survey) to see whether the model still works today.
4. **Add blood-test results** such as blood sugar, if available, to see how much more accurate the model can become.
5. **Run a small pilot** in a clinic: follow up the people the model flags, and measure how many really have diabetes and what the extra testing costs.

## Repository contents

```
diabetes-risk-capstone/
├── README.md                              (this report)
├── data/
│   └── diabetes_binary_health_indicators_BRFSS2015.csv
├── notebooks/
│   ├── 01_initial_report_eda.ipynb        (cleaning, charts and first model)
│   └── 02_final_models_and_report.ipynb   (model comparison, tuning, threshold, fairness check)
└── reports/
    └── Initial_Report_Summary.docx        (summary of the initial report)
```

## How to run

1. Install Python 3.10+ and the packages `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn` and `jupyter`.
2. Open the notebooks in the `notebooks/` folder in Jupyter and choose **Run All**, first `01_...` and then `02_...`. Both read the data from `../data/`. Notebook 2 takes a few minutes because of the model tuning.
