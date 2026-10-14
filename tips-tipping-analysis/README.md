# Restaurant Tipping Behavior Analysis (Tips Dataset)

Minor project: end-to-end EDA of the `seaborn` **tips** dataset (244 rows, 7 columns) to answer a restaurant manager's questions about tipping behavior and give actionable recommendations.

## Business goals
1. Train staff to maximise tips
2. Identify which customer segments tip best
3. Decide whether to add automatic gratuity for large parties

## Project structure
```
tips-tipping-analysis/
├── notebooks/tips_analysis.ipynb   # full analysis (cleaning → features → EDA → stats → modelling)
├── figures/                        # charts exported by the notebook
├── data/tips.csv                   # copy of the dataset
├── requirements.txt
└── README.md
```

## How to run
```bash
python -m venv .venv && source .venv/bin/activate     # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/tips_analysis.ipynb
```
Data loads with `sns.load_dataset('tips')` (needs internet the first time); `data/tips.csv` is included as a backup.

## Methodology
- **Cleaning:** no missing values, no impossible values; 1 duplicate row kept (no ID column, so identical parties are plausible).
- **Features engineered:** `tip_pct`, `bill_per_person`, `tip_per_person`, `is_weekend`, `party_group`, `bill_bracket`, `tip_class`.
- **EDA + stats:** group summaries, Welch t-test, Mann-Whitney U, ANOVA, Kruskal-Wallis, Pearson/Spearman correlation, IQR outlier detection.
- **Modelling:** Linear Regression and Random Forest (tip $), OLS (tip %), 5-fold CV.

## Key findings

| # | Question | Answer |
|---|----------|--------|
| 1 | Average tip % | **Mean 16.1%, median 15.5%** (95% CI 15.3–16.9%). Inside the 15–20% norm, but only just above 15%. 39% of parties tip 15–20%, 34% tip 10–15%, 11% tip under 10%, 16% tip over 20%. |
| 2 | Men vs women | Men tip slightly more in dollars ($3.09 vs $2.83) because their bills are bigger ($20.74 vs $18.06). Women tip a slightly higher %, (16.7% vs 15.8%). **Neither tip difference is statistically significant** (p = 0.14, 0.25). |
| 3 | Smokers vs non-smokers | **No meaningful difference**: $3.01 vs $2.99, 16.3% vs 15.9% (p = 0.93, 0.68). |
| 4 | Best day | **Sunday** has the highest average tip ($3.26); **Saturday** collects the most total tips ($260) due to volume. Tip % is flat across days (15.3–17.0%); differences are **not significant** (ANOVA p = 0.17 for $, 0.47 for %). |
| 5 | Dinner vs lunch | Dinner is more lucrative per party ($3.10 vs $2.73, p = 0.04) and in total ($546 vs $186), but this is driven by bigger bills. **Tip % is the same** (16.0% vs 16.4%). Caveat: Sat/Sun have no lunch data. |
| 6 | Big-group myth | Partly true. Tip % drifts down with size (Spearman ρ = -0.15, p = 0.017): 16.6% for 2 people, 14.6% for 4, ~14% for 5+. But the dollar tip rises (r = 0.49) and the group-level difference is not significant (Kruskal p = 0.13). Only 9 parties have 5+ people. |
| 7 | Outliers | 4 tip-% outliers (IQR rule), all with tiny bills: one 71% tip on a $7.25 bill, one 41.7%, 32.6% and 29.2%. 27 parties (11%) tip under 10%. 9 parties have unusually large dollar tips (>$5.91), mostly Saturday/Sunday dinner parties of 3–4 with bills of $30+. |
| 8 | Best predictors | **Total bill dominates** (RF importance 84%, r = 0.68 with tip). Party size is second. Sex, smoker, day and time add little. Models explain only ~35–40% of tip $ variance (CV R² ≈ 0.40–0.42, MAE ≈ $0.70). Highest-tipping segment: **Sunday dinner with a smoker in the party** (avg $3.52, 18.7%, n = 19, a small sample). |

Additional insight: tip % **falls as the bill rises** (r = -0.34), so higher-spend tables tip a smaller share.

## Recommendations
1. **Focus on increasing bill size, not on customer demographics.** Bill is the strongest predictor of tip $. Train staff in suggestive selling (starters, desserts, drinks, sharing plates). Gender and smoking status are not reliable segments to target.
2. **Prioritise staffing on Saturday and Sunday dinner.** They produce most of the total tip revenue; assign the strongest servers there.
3. **Automatic gratuity for large parties: lean towards a modest, optional trial, not a blanket rule.** Large parties tip ~14–15% vs ~16.7% for small ones, which supports it, but the evidence is weak (9 parties, not statistically significant). Trial 15–18% for parties of 5+ in selected branches and compare.
4. **Coach staff on 3–4 person tables.** Their tip % is about 2 points below pairs. Examples: check-ins, attentive service, and showing suggested tip amounts on the bill.
5. **Don't treat outliers as typical.** Very high tip % comes from very small bills; low-tip cases (11% under 10%) are worth reviewing for service issues.
6. **Collect more data** (several servers/branches, server ID, order items, service rating, payment method) before making chain-wide policy changes.

## Limitations
- Data from **one server** at one restaurant; may not generalise to the chain.
- Small samples for Fridays (19), parties of 1 and 5+ (4–9 each), and Sat/Sun lunch (none).
- Observational data shows association, not causation.

## Tech stack
Python, pandas, NumPy, seaborn, matplotlib, SciPy, scikit-learn, statsmodels, Jupyter.
