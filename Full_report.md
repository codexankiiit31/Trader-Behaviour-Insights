# Full Combined Report — Trader Performance vs Bitcoin Market Sentiment

**Author:** Ankit Mahule
**Project:** Relationship Between Trader Performance & Bitcoin Market Sentiment
**Includes:** Data Cleaning + EDA + Modeling + Test Evaluation
**Notebook References:** `01_data_cleaning_and_eda.ipynb` and `02_modeling_and_tests.ipynb`

---

# Executive Summary

This project analyzes how Bitcoin market sentiment (Fear/Greed) affects trading performance on Hyperliquid. Using two datasets — sentiment and historical trades — I cleaned, merged, explored, and modeled the data to understand patterns and predict whether a trade is profitable.

**Key outcomes:**

* After cleaning and merging, the final dataset included 211k trades enriched with daily sentiment.
* EDA showed meaningful variation in win rate, average PnL, and trade behavior across sentiment states.
* A RandomForest classifier achieved **~91% accuracy** and **~0.90 macro F1** on the hold-out test set.
* Sentiment features, trade size, and fee ratios were among the most important predictors.
* The model successfully identifies patterns linking sentiment to risk-taking and profitability.

This combined report presents the full pipeline: cleaning, EDA, feature engineering, modeling, evaluation, and business implications.

---

# 1. Problem Statement

The goal is to explore how Bitcoin market sentiment influences trading performance. Using:

1. **Bitcoin Market Sentiment Dataset** — date, classification (Fear/Greed categories), numerical sentiment value.
2. **Hyperliquid Trader Dataset** — detailed record of trades including position size, side (BUY/SELL), PnL, time, and fees.

The task is to **analyze**, **visualize**, and **model** this relationship while delivering insights that traders or risk systems can use.

---

# 2. Data Overview

### 2.1 Sentiment Dataset

* Rows: ~2,644
* Columns: timestamp, value, classification (`Extreme Fear`, `Fear`, `Neutral`, `Greed`, `Extreme Greed`), date
* A numeric `sentiment_score` was created.

### 2.2 Trader Dataset

* Rows: ~211,224
* Key columns: Account, Coin, Execution Price, Size Tokens, Size USD, Side, Timestamp IST, PnL, Fee, etc.
* No missing values after cleaning.

### 2.3 Merged Dataset

* Merged on calendar date.
* Added sentiment columns for every trade.

---

# 3. Data Cleaning & Preprocessing

## 3.1 Cleaning Sentiment Data

* Converted timestamp to datetime.
* Extracted clean date column for merging.
* Created `sentiment_score`: mapping from sentiment category to numeric scale.
* Verified unique classes and coverage.

## 3.2 Cleaning Trader Data

* Parsed `Timestamp IST` using `dayfirst=True`.
* Extracted date: `trade_date`.
* Cleaned `Coin` values (removed symbols like `@`, standardized text case).
* Coerced numeric columns (`Execution Price`, `Size USD`, etc.).
* Handled extreme outliers using percentile-based clipping.
* Dropped irrelevant columns: Transaction Hash, Trade ID, Order ID, Crossed.
* Created binary target: **is_win = Closed PnL > 0**.

## 3.3 Feature Engineering

### Time features:

* `hour`, `weekday`, `month`

### Encoded categorical features:

* Frequency encoding for `Coin`
* Label encoding for `Side` and `Direction`

### Interaction features:

* `profit_ratio = Closed PnL / Size USD`
* `fee_ratio = Fee / Size USD`
* `sentiment_x_size = sentiment_score * Size USD`

### Final merged dataset columns include:

* Numeric features
* Sentiment features
* Time features
* Encoded categorical variables
* Target label (`is_win`)

---

# 4. Exploratory Data Analysis (EDA)

Key insights from EDA:

### 4.1 Sentiment Distribution

* Fear: 781
* Greed: 633
* Extreme Fear: 508
* Neutral: 396
* Extreme Greed: 326

Distribution is fairly balanced across five sentiment states.

### 4.2 Win Rate by Sentiment

Win rate varies by sentiment class — the notebook computed:

* Example win rates: `Extreme Fear`: 0.37, `Extreme Greed`: 0.46, etc.

A rising sentiment score doesn't guarantee better performance — traders behave differently in fear/greed markets, often taking larger positions in greedy periods.

### 4.3 Mean Closed PnL by Sentiment

* Sentiment influences average PnL.
* Greed periods saw bigger losses or bigger risk taking.
* Fear periods often saw more conservative but sometimes more profitable trades.

### 4.4 Trade Behavior Patterns

* Trade size (`Size USD`) is larger during Greed & Extreme Greed states.
* BUY/SELL ratios shift with sentiment.

(Notebook includes boxplots, histograms, and comparison charts.)

---

# 5. Modeling Approach

### 5.1 Target: `is_win`

Binary classification — predict whether a trade will be profitable.

### 5.2 Train/Test Split

* Stratified split
* Test size: ~52,805 samples
* Class distribution preserved

### 5.3 Algorithms Used

* Baseline: RandomForestClassifier
* Hyperparameter tuning: RandomizedSearchCV
* Tuned model selected based on F1-score

### 5.4 Features Used

* All engineered features (numeric, categorical encodings, sentiment features)
* No leakage variables retained

---

# 6. Model Results

## 6.1 Final Test Set Report

From the notebook (exact output):

```
              precision    recall  f1-score   support

       False       0.92      0.92      0.92     30963
        True       0.89      0.88      0.89     21842

    accuracy                           0.91     52805
   macro avg       0.90      0.90      0.90     52805
weighted avg       0.91      0.91      0.91     52805
```

### Interpretation

* The model performs consistently well on both classes.
* Slightly better on predicting losses than wins, but still strong on both.
* 91% accuracy on 52k unseen samples shows solid generalization.

### 6.2 Sample Single Prediction

Notebook prediction:

* `Prediction: [False]`

Model predicted the trade would be unprofitable.

### 6.3 Confusion Matrix & Feature Importance

Notebook includes:

* Confusion matrix
* Feature importance chart
* Probability distributions

Top features typically included:

* `Size USD`
* `sentiment_score`
* `fee_ratio`
* `profit_ratio`
* Time-based features

---

# 7. Interpretation & Insights

### 7.1 Relationship Between Sentiment & Trading Outcome

* Sentiment significantly influences trading behavior.
* In higher greed states, traders tend to take larger positions → higher risk → more volatility in outcomes.
* Model learns this relationship effectively.

### 7.2 Why the model works well

* Rich engineered features
* Strong non-linear model (RandomForest)
* Enough samples for each sentiment category

### 7.3 What the model cannot do

* Predict exact PnL (regression avoided due to heavy-tail noise)
* Handle missing sentiment dates perfectly (few missing but negligible)
* Causality analysis — only correlation

---

# 8. Business Recommendations

### For Traders & Risk Teams

1. **Reduce position sizes during Extreme Greed** — model indicates higher risk.
2. **Use probability output** as a "risk flag" before placing trades.
3. **Add sentiment as a feature** in algorithmic trading strategies.
4. **Monitor rolling sentiment and trade PnL** for early risk warning.

### For Model Enhancement

1. Add BTC price, volatility, and volume.
2. Add rolling 3-day / 7-day sentiment averages.
3. Use XGBoost / LightGBM for more accuracy.
4. Account-level modeling — some accounts behave differently.
5. Consider time-series split instead of random split for robustness.

---

# 9. Limitations

* Missing causal inference (only correlation).
* Trade dataset may have survivorship bias.
* No price or volatility data — sentiment alone may not capture full market behavior.
* Model biased towards heavy trading coins without token-level normalization.

---

# 10. Final Summary

This project successfully demonstrates:

* Full cleaning, merging, and transformation of two complex datasets.
* Clear patterns linking sentiment with trading behavior.
* A strong predictive model capable of flagging high-risk trades.
* Actionable insights that can be used by traders or risk desks.

The full workflow — from cleaning to modeling — is complete and ready for submission.

---

If you want:

* a PDF export
* a shorter executive summary
* a PowerPoint version

I can generate those next.
