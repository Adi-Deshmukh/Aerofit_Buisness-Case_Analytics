# Aerofit Treadmill — Customer Profiling & Product Analytics

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-2.x-150458?style=flat-square&logo=pandas&logoColor=white)
![Seaborn](https://img.shields.io/badge/Seaborn-0.13-4EACD5?style=flat-square)
![SciPy](https://img.shields.io/badge/SciPy-Stats-8CAAE6?style=flat-square&logo=scipy&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-2ea44f?style=flat-square)

> **Business Context:** Aerofit is a fitness equipment brand selling three treadmill models — KP281 (entry), KP481 (mid), and KP781 (premium). This project conducts a full customer profiling and product analytics study to enable data-driven marketing, segmentation, and product positioning decisions.

---

## Table of Contents

- [Problem Statement](#problem-statement)
- [Dataset](#dataset)
- [Project Structure](#project-structure)
- [Methodology](#methodology)
- [Key Findings](#key-findings)
- [Customer Segments](#customer-segments)
- [Strategic Recommendations](#strategic-recommendations)
- [Tech Stack](#tech-stack)
- [How to Run](#how-to-run)

---

## Problem Statement

Aerofit's market research team wants to identify the **characteristics of the target audience** for each treadmill product to improve product recommendations and customer acquisition strategies.

**Goals:**
- Understand which customer profiles drive purchases for each treadmill model
- Quantify conditional probabilities of product choice given customer characteristics
- Surface actionable segmentation and positioning insights for business teams

---

## Dataset

| Feature | Description |
|---|---|
| `Product` | Treadmill model purchased — KP281, KP481, KP781 |
| `Age` | Customer age in years |
| `Gender` | Male / Female |
| `Education` | Years of formal education |
| `MaritalStatus` | Single / Partnered |
| `Usage` | Average weekly usage (days/week) |
| `Fitness` | Self-rated fitness score (1–5) |
| `Income` | Annual income (USD) |
| `Miles` | Expected weekly mileage |

**Size:** 180 records × 9 features — No missing values

---

## Project Structure

```
aerofit-analytics/

 Aerofit_BC.ipynb          # Full analysis notebook
 
 README.md
 
```

---

## Methodology

### 1. Data Preparation
- Type casting: Fitness and Education converted to `category`, continuous variables to `float64`
- Feature engineering: `AgeGroup` (pd.cut), `IncomeGroup` (pd.qcut — equal frequency), `MilesGroup` (pd.cut)
- Outlier treatment: IQR-based detection → 5th–95th percentile **clipping** on Income and Miles

>  **Design Decision:** Clipping (not dropping) was chosen to preserve all 180 records while reducing the influence of extreme values. Income had 19 outliers (10.5%) — dropping them would have introduced significant sample bias.

### 2. Univariate Analysis
- Count plots + pie charts for all categorical variables
- Descriptive statistics (mean, std, quartiles) for continuous variables

### 3. Bivariate Analysis — Numerical vs. Product

| Feature | Normality (Shapiro) | Variance (Levene) | Test Used | Result |
|---|---|---|---|---|
| Age |  Not Normal |  Equal | Kruskal-Wallis | No significant effect |
| Income |  Not Normal |  Unequal | Kruskal-Wallis | **Significant effect** (p ≈ 4.6e-14) |
| Usage | — | — | Kruskal-Wallis | **Significant effect** (p ≈ 1.2e-15) |
| Miles | — | — | Kruskal-Wallis | **Significant effect** (p ≈ 1.2e-15) |

### 4. Bivariate Analysis — Categorical vs. Product

| Feature | Test | Result |
|---|---|---|
| Gender | Chi-Square | **Associated** (p = 0.0016) |
| MaritalStatus | Chi-Square | Not associated (p = 0.96) |
| IncomeGroup | Chi-Square | **Associated** (p ≈ 5.3e-13) |
| AgeGroup | Chi-Square | Not associated (p = 0.95) |

### 5. Probability Analysis
- Marginal probabilities: `P(Product)`
- Forward conditional: `P(Product | Feature)` — customer-to-product mapping
- Reverse conditional: `P(Feature | Product)` — product-to-customer profiling

### 6. Correlation Analysis
Pearson correlation on numerical features revealed strong multicollinearity in the behavioral cluster:

```
Miles ↔ Fitness:  0.82  (strong)
Miles ↔ Usage:    0.79  (strong)
Usage ↔ Fitness:  0.67  (moderate-strong)
Income ↔ Miles:   0.54  (moderate)
Age ↔ Income:     0.51  (moderate)
```

---

## Key Findings

### Product Distribution
```
KP281    44.4%
KP481        33.3%
KP781            22.2%
```

### Income is the Strongest Purchase Driver
| Income Group | KP281 | KP481 | KP781 |
|---|---|---|---|
| Low | 61.9% | 38.1% | **0.0%** |
| Medium | 48.4% | 38.7% | 12.9% |
| High | 20.0% | 21.8% | **58.2%** |

No low-income customer purchased KP781. Conversely, 80% of KP781 buyers are high-income.

### Gender Drives Premium Adoption
- KP781 buyers: **82.5% male** — the most skewed product by gender
- KP281 buyers: 50/50 gender split — broadest appeal
- Male customers are **3.3× more likely** than female customers to purchase KP781

### Usage Intensity Segments the Market
- KP781 users run **4–7 days/week** exclusively
- KP281/KP481 users cluster at **2–4 days/week**
- No statistical age effect on product choice (p = 0.835, Kruskal-Wallis)

### Behavioral Cluster (Usage + Fitness + Miles)
These three variables are tightly correlated and collectively define a "performance orientation" latent factor that strongly predicts KP781 purchase.

---

## Customer Segments

### KP281 — Mass / Entry Segment
```
Income:   Low–Medium     Usage:   Low–Moderate
Mileage:  Low            Gender:  Balanced
Behavior: Casual / beginner users
```
**Business role:** High volume, low margin. Primary acquisition product.

---

### KP481 — Transitional / Undifferentiated Segment
```
Income:   Medium         Usage:   Moderate
Mileage:  Moderate       Fitness: Lower than expected
Behavior: Regular but not performance-driven
```
**Business risk:** Weak positioning. Not affordable enough to attract beginners, not capable enough to attract serious users. → **At risk of being bypassed.**

---

### KP781 — Premium / Performance Segment
```
Income:   High           Usage:   High (4–7 days/week)
Mileage:  High           Fitness: High (avg 4.625/5)
Gender:   Male-skewed    Age:     22–30 years
```
**Business role:** High margin, low volume. Underpenetrated in female and mid-age segments.

---

## Strategic Recommendations

### 1. Fix KP481 Positioning (Critical)
KP481 has the lowest fitness engagement of all three products and is being bypassed by users who go directly from KP281 to KP781. **Options:**
- Reposition as the "serious regular user upgrade" with clearer feature differentiation
- Conduct exit-intent surveys to understand why KP781 buyers didn't consider KP481
- Consider pricing restructure or feature additions to justify the mid-tier slot

### 2. Build an Upsell Funnel
The large KP281 base is an untapped upsell opportunity. Trigger-based upgrade nudges:
```
KP281 user increases usage to 4+ days/week → prompt KP481 consideration
KP481 user sustains high mileage           → prompt KP781 upgrade
```

### 3. Expand Female Premium Adoption
Only 9.2% of female customers purchase KP781 (vs. 31.7% of males). Targeted actions:
- Female-specific performance campaigns (health outcomes, not just performance)
- Investigate whether the gap is driven by price sensitivity, product design, or marketing reach
- Focus on 40+ female segment where mileage dips sharply — engagement opportunity

### 4. Deploy a Recommendation Engine
A simple rule-based recommender using the three strongest predictors:

| Income | Usage | Mileage | → Recommend |
|---|---|---|---|
| High | High | High | KP781 |
| Medium | Medium↑ | Medium | KP481 |
| Any | Low | Low | KP281 |

This can evolve into a probabilistic model (e.g., logistic regression or Random Forest) once more data is available.

### 5. EMI / Financing for Medium-Income Segment
Medium-income customers show 13% conversion to KP781 despite having partial capacity. Financing options could unlock this segment, which represents a significant addressable market.

---

## Tech Stack

| Library | Purpose |
|---|---|
| `pandas` | Data wrangling, crosstabs, groupby aggregations |
| `numpy` | Clipping, array operations |
| `matplotlib` / `seaborn` | Univariate, bivariate, and multivariate visualizations |
| `scipy.stats` | Shapiro-Wilk, Levene, One-Way ANOVA, Kruskal-Wallis, Chi-Square |
| `statsmodels` | Q-Q plots for normality visual verification |

---

## How to Run

```bash
# Clone the repository
git clone https://github.com/<your-username>/aerofit-analytics.git
cd aerofit-analytics

# Install dependencies
pip install -r requirements.txt

# Launch notebook
jupyter notebook notebooks/Aerofit_BC.ipynb
```

**requirements.txt**
```
pandas>=2.0
numpy>=1.24
matplotlib>=3.7
seaborn>=0.13
scipy>=1.11
statsmodels>=0.14
```

---

## Notes & Limitations

- **Sample size (n=180):** Chi-square tests and Kruskal-Wallis are valid but effect size estimates carry wide confidence intervals. Findings should be validated on a larger sample before acting on them.
- **Self-reported features:** Fitness score and expected mileage are self-reported and may carry response bias.
- **No temporal data:** Purchase trends over time, seasonality, or repeat purchases cannot be analyzed with this dataset.
- **Causality:** All findings are associative. Income driving KP781 purchase does not rule out confounders (e.g., fitness culture, brand awareness by income bracket).

---

## Author

**Aditya Deshmukh**  
Final Year — Computer Science & Data Science Engineering  
 [Adideshmukh2005@gmail.com] ·  [[LinkedIn](https://www.linkedin.com/in/aditya-deshmukh-2587ab1ba)] ·  [[GitHub](https://github.com/Adi-Deshmukh)]

---

*If you found this analysis useful, please consider leaving star on the repo.*
