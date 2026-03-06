# 📊 Predicting Student Stress Using Wearable and Survey Data

### NetHealth Study | Multimodal Stress Prediction

> Investigating whether student stress levels can be predicted using behavioral data from wearables and self-reported survey responses.

---

## 📑 Table of Contents

- [Overview](#overview)
- [Dataset](#dataset)
- [Modeling Pipelines](#modeling-pipelines)
  - [Wearable-Only Model](#1-wearable-only-model)
  - [Survey-Only Model](#2-survey-only-model)
  - [Combined Model](#3-combined-wearable--survey-model)
- [Key Findings](#key-findings)
- [Repository Structure](#repository-structure)
- [Future Work](#future-work)

---

## 🎯 Overview

This project implements **three modeling pipelines** to predict perceived stress scores (Perceived Stress Scale - PSS):

| Model Type        | Description                                        |
| :---------------- | :------------------------------------------------- |
| **Wearable-only** | Uses Fitbit activity and sleep metrics             |
| **Survey-only**   | Uses psychological and behavioral survey responses |
| **Combined**      | Integrates wearable + survey features              |

**Goal:** Evaluate the predictive value of passive sensing data versus self-reported measures.

---

## 📦 Dataset

The analysis uses data from the **NetHealth study**, a longitudinal behavioral dataset tracking university students across multiple waves.

### 📋 Data Sources

#### 1️⃣ Basic Survey Data

- **3000+ variables** across 8 survey waves
- Stress measured in waves: **4, 5, 6, and 8**
- Variables include: happiness, health perception, sleep quality (PSQI), exercise, substance use, interpersonal trust

#### 2️⃣ Fitbit Activity Data

Daily metrics aggregated into mean and variability measures:

```
• steps               • sedentary minutes
• floors climbed      • active minutes
• heart rate          • calories burned
```

#### 3️⃣ Fitbit Sleep Data

```
• sleep duration      • time to fall asleep
• time awake          • sleep efficiency
```

---

## 🤖 Modeling Pipelines

### 1️⃣ Wearable-Only Model

**Feature Engineering:** Aggregated daily Fitbit data using a **30-day rolling window** before each survey date.

**Features Generated:**

```
steps_mean, steps_std
sedentaryminutes_mean, sedentaryminutes_std
minsasleep_mean, minsasleep_std
efficiency_mean, efficiency_std
...
```

**Data Quality:** Students with < 7 wearable days excluded from analysis.

**Train/Test Split:** GroupShuffleSplit by student (egoid) → **no student overlap between sets**

**Models Tested:**

- Dummy Regressor (baseline)
- Linear Regression
- Random Forest
- XGBoost

**⚠️ Results:**

> Limited predictive performance. Passive sensing signals alone are weak stress predictors.

---

### 2️⃣ Survey-Only Model

**Predictor Variables:**

```
happiness              exercise frequency    sleep quality (PSQI)
health perception     sedentary behavior     alcohol use
interpersonal trust   caffeine use           tobacco use
```

**Encoding:** Categorical variables encoded using OrdinalEncoder

**Train/Test Split:** GroupShuffleSplit by student (egoid)

**Models Tested:**

- Dummy Regressor
- Random Forest
- XGBoost

**✅ Best Performance:**

```
Model:   XGBoost
MAE:     0.44 (lower is better)
R²:      0.40 (explains 40% of variance)
```

**Top Predictive Variables:**

1. Happiness
2. Trust
3. Sleep quality
4. Health perception

---

### 3️⃣ Combined Wearable + Survey Model

**Data Integration:** Merged wearable features with survey predictors on `(egoid, wave)`

**Final Dataset:**

- ~900 observations (student-wave pairs)
- ~60 features

**Models Tested:**

- Dummy Regressor
- Random Forest
- XGBoost

**📊 Best Performance:**

```
Model:   Random Forest
MAE:     0.50
R²:      0.23 (explains 23% of variance)
```

**Insight:** Combined models improved over wearable-only but didn't outperform survey-only models. Survey variables remained dominant predictors.

---

## 🔍 Key Findings

| Finding                                            | Impact                                                   |
| :------------------------------------------------- | :------------------------------------------------------- |
| **Self-reported psychological variables dominate** | Happiness, trust, sleep quality are strongest predictors |
| **Wearable data alone insufficient**               | Fitbit metrics have limited stress predictive power      |
| **Combined approach outperforms wearables alone**  | But survey data carries most explanatory power           |
| **Behavioral variability matters**                 | Std dev of activity metrics contribute moderate signal   |

---

## 📁 Repository Structure

```
data_model_project/
├── README.md                          # This file
├── data/
│   ├── BasicSurvey.csv               # Survey responses (3000+ variables, 8 waves)
│   ├── FitbitActivity.csv            # Daily activity metrics
│   ├── FitbitSleep.csv               # Daily sleep data
│   ├── survey_features.csv           # Engineered survey features
│   ├── wearable_features_7d.csv      # 7-day aggregated wearable features
│   └── wearable_features_30d.csv     # 30-day aggregated wearable features
│
└── notebooks/
    ├── exploration.ipynb              # Data exploration & visualization
    ├── wearables_model.ipynb          # Wearable-only modeling pipeline
    ├── survey_model.ipynb             # Survey-only modeling pipeline
    └── multimodal_stress_model.ipynb  # Combined wearable + survey model
```

---

## 🚀 Future Work

- [ ] **Temporal sequence modeling** - Leverage time-series structure for predictions
- [ ] **Personalized models** - Student-specific stress models
- [ ] **Deep learning** - Multimodal neural networks (CNN/RNN on sensor + survey data)
- [ ] **Feature engineering** - Circadian rhythm features from wearables
- [ ] **Causality analysis** - Which factors causally influence stress?
- [ ] **Cross-study validation** - Test on external datasets

---

## 📝 Citation

If you use this analysis, please cite:

```
NetHealth Study Analysis: Predicting Student Stress (2024)
```
