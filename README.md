📊 Trader Performance vs Bitcoin Market Sentiment
Full Analysis · Data Cleaning · EDA · Modeling · Evaluation

This project analyzes how Bitcoin market sentiment (from Extreme Fear → Extreme Greed) impacts trader performance on Hyperliquid.
It includes a complete pipeline from data cleaning → feature engineering → machine learning modeling → evaluation.

📁 Project Structure
project/
│── 01_data_cleaning_and_eda.ipynb
│── 02_modeling_and_tests.ipynb
│── merged_final_dataset.csv
│── README.md
│
└── reports/
      └── full_combined_report.pdf   (optional)

🚀 Objective

Merge daily Bitcoin sentiment data with Hyperliquid trading data

Explore how sentiment influences trade profitability

Build a model to predict profitable trades (is_win)

Generate insights that support smarter trading and risk decisions

🧠 Datasets
1. Bitcoin Market Sentiment Dataset

Includes:

timestamp

value

classification (Extreme Fear → Extreme Greed)

date

Derived:

sentiment_score (numeric encoding)

2. Hyperliquid Trader Dataset

Includes:

Account, Coin

Execution Price, Size Tokens, Size USD

Side (BUY/SELL), Direction

Closed PnL, Fee

Timestamp IST

Derived:

is_win = Closed PnL > 0

3. Final Merged Dataset

Merged using:

trade_date == sentiment_date

🧹 Data Cleaning Pipeline
✔ Sentiment Dataset

Clean timestamp formats

Extract proper dates

Encode sentiment → sentiment_score

Check full date coverage

✔ Trader Dataset

Parse Timestamp IST (dayfirst=True)

Clean Coin names (remove '@')

Convert numeric columns

Drop unnecessary columns:

Transaction Hash

Trade ID

Order ID

Crossed

Handle outliers using IQR/percentile clipping

Create binary target: is_win

🧩 Feature Engineering
Time Features

hour

weekday

month

Encodings

coin_freq

side_le

dir_le

Interaction & Ratio Features

sentiment_x_size

fee_ratio = Fee / Size USD

profit_ratio = Closed PnL / Size USD

🔍 Exploratory Data Analysis (EDA)
📌 Key Insights

Sentiment distribution covers all 5 categories

Win rate varies strongly across sentiment classes

Trade sizes increase during Greed periods

PnL variance is highest in Extreme Greed

Side (BUY/SELL) ratios shift with sentiment

📊 Visuals in Notebook

Win rate charts

Mean PnL per sentiment

Boxplots

Histograms

Sentiment frequency

Trade size distributions

🤖 Machine Learning Modeling
🎯 Target
is_win → 1 (profitable), 0 (loss)

🧪 Models

RandomForestClassifier

RandomizedSearchCV tuning

Scored using F1

🧱 Features Used

Trade numeric features

Encoded categorical fields

Sentiment-based features

Time features

Interaction features

📚 Train/Test Split
Train : 80%
Test  : 20%
Total test samples: 52,805


Stratified by target.

🧪 Final Model Performance
              precision    recall  f1-score   support

       False       0.92      0.92      0.92     30963
        True       0.89      0.88      0.89     21842

    accuracy                           0.91     52805
   macro avg       0.90      0.90      0.90     52805
weighted avg       0.91      0.91      0.91     52805

⭐ Highlights

91% accuracy

0.90 macro F1

Balanced prediction across both classes

Example Prediction
Prediction: [False]

📈 Feature Importance

Key drivers of profitable trades:

Size USD

Sentiment Score

Fee Ratio

Profit Ratio

Hour, Weekday

Coin Frequency

💡 Key Insights

Extreme Greed → Bigger positions → Higher volatility

Extreme Fear → Smaller positions → More stable results

Sentiment + position size strongly predicts win/loss

Fees and trade timing matter significantly

🛠️ Recommendations
For Traders

Reduce exposure in Extreme Greed

Use model predictions as pre-trade risk signals

Track fee ratios & time-based patterns

For Model Improvements

Add BTC volatility & price movement as features

Add rolling 3-day / 7-day sentiment metrics

Try XGBoost / LightGBM models

Add account-level behavior patterns

Use time-series validation splits

🧩 Limitations

Does not infer causality, only correlation

No BTC price context included

Possible dataset gaps

PnL has a heavy-tailed distribution

▶️ How to Run the Project
1️⃣ Install Requirements
pip install -r requirements.txt

2️⃣ Run the Notebooks
jupyter notebook


Open and run:

01_data_cleaning_and_eda.ipynb

02_modeling_and_tests.ipynb

👤 Author

Ankit Mahule
Machine Learning • Data Analysis
Crypto Analytics | Hyperliquid Insights
