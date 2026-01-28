# Sentiment Analysis for Stock Market Prediction

## Problem Statement

This project builds a machine learning pipeline to predict stock market movements (specifically NIFTY 50 index) using sentiment analysis from financial news headlines and articles. We investigate whether market sentiment extracted from news sources has predictive power for short-term (1-day) price movements.

### Hypothesis
Financial news sentiment influences investor behavior and market movements. By aggregating daily sentiment scores from multiple financial news sources, we can build a signal that predicts next-day stock market returns.

---

## Prediction Target

**Binary/Ternary Classification Problem:**
- **Target Variable:** `signal` (1-day forward return classification)
  - `1` (Positive): Expected next-day return > 0%
  - `-1` (Negative): Expected next-day return < 0%
  - `0` (Neutral): Expected next-day return ≈ 0%

**Underlying Metric:** Daily percentage return of NIFTY 50 index
- Calculated as: `(Close_today - Close_yesterday) / Close_yesterday * 100`

**Prediction Horizon:** 1 day ahead (t+1 prediction from t observation)

---

## Data Sources

### Raw Data (`/data_raw/`)
- **rss_articles.csv**: Financial news headlines and articles from multiple sources
  - **Sources:** Economic Times, Moneycontrol, Mint, Times of India, Hindustan Times, etc.
  - **Time Period:** Historical data from multiple years
  - **Columns:**
    - `source`: News outlet (categorical)
    - `timestamp`: Publication datetime
    - `title`: Article headline
    - `text_body`: Article content/summary
    - `author`: Article author
    - `url`: Source URL

- **NIFTY 50 Index Data**: Downloaded via `yfinance` library
  - `Close`: Daily closing price
  - `Date`: Trading date

### Processed Data (`/data_processed/`)
Will contain cleaned, feature-engineered datasets ready for modeling

---

## Key Assumptions

### 1. **Sentiment Analysis Validity**
   - FinBERT (fine-tuned BERT for financial text) accurately captures sentiment in financial news
   - Three-class sentiment (Positive/Neutral/Negative) adequately represents market sentiment
   - Sentiment scaling: `score × label_numeric` appropriately weights intensity

### 2. **Time Series Assumptions**
   - Market sentiment is aggregated per trading day (mean of daily articles)
   - Sentiment on date `t` predicts return on date `t+1` (1-day lag assumption)
   - No look-ahead bias: articles published after market close are excluded

### 3. **Data Quality**
   - RSS feeds capture representative sample of financial news
   - No duplicate articles or significant missing data in core features
   - Text preprocessing (stopword removal, length truncation to 512 tokens) preserves semantic meaning

### 4. **Market Assumptions**
   - NIFTY 50 daily close is reliable and unadjusted
   - 1-day holding period is the relevant forecast horizon
   - Market is semi-efficient (sentiment has predictive power but not perfectly priced in)

### 5. **Model Assumptions**
   - Feature engineering based on daily sentiment aggregates is sufficient
   - No hidden confounding variables (e.g., macroeconomic events not captured in news)
   - Past performance relationships hold in future periods (stationarity assumption)
   - Train/test split maintains temporal order (no future leakage)

### 6. **Statistical Assumptions**
   - Training features are approximately normally distributed after scaling
   - Feature-target relationships are reasonably linear (or captured by ensemble methods)
   - Imbalanced classes (sentiment distribution) don't severely bias model performance

---

## Methodology

### Pipeline Overview
```
1. Data Collection & Cleaning
   ├── Load RSS articles
   ├── Merge with market data
   └── Handle missing values

2. Text Processing
   ├── Remove stopwords
   ├── Tokenize with FinBERT
   └── Truncate to 512 tokens

3. Sentiment Analysis
   ├── Load FinBERT model
   ├── Extract sentiment labels & scores
   └── Aggregate daily sentiment

4. Feature Engineering
   ├── Daily sentiment aggregation (mean)
   ├── Lagged features (future work)
   └── Market features (future work)

5. Modeling & Evaluation
   ├── Logistic Regression (baseline)
   ├── Random Forest
   ├── XGBoost
   ├── Neural Networks (MLP)
   ├── Gradient Boosting
   └── SVM
```

### Models Implemented
1. **Logistic Regression** - Linear baseline
2. **Random Forest Classifier** - Ensemble tree-based
3. **XGBoost Classifier** - Gradient boosted trees
4. **Neural Networks (MLPClassifier)** - 2-layer MLP
5. **Gradient Boosting Classifier** - sklearn gradient boosting
6. **Support Vector Machine (SVM)** - Non-linear classifier

### Evaluation Metrics
- **Accuracy:** Overall correct predictions
- **Precision/Recall:** Per-class performance
- **Classification Report:** Detailed per-class metrics
- *Future: Directional accuracy, profit/loss (if backtested)*

---

## Project Structure

```
sentiment-analysis-stock-prediction/
│
├── README.md                 # This file
├── .gitignore               # Git ignore rules
│
├── notebooks/               # Exploration & analysis (use for EDA only)
│   ├── filtered_headlines.ipynb      # Data exploration & preprocessing
│   └── Models1.ipynb                 # Model development & training
│
├── src/                     # Production-ready code (modules & pipelines)
│   ├── __init__.py
│   ├── config.py           # Configuration & constants
│   ├── data_loader.py      # Data loading & I/O
│   ├── preprocessing.py    # Text cleaning & feature engineering
│   ├── sentiment_analysis.py # FinBERT sentiment extraction
│   ├── modeling.py         # Model training & evaluation
│   └── utils.py            # Helper functions
│
├── data_raw/                # Raw, unprocessed data (git-ignored if large)
│   ├── rss_articles.csv
│   └── rss_articles.parquet
│
├── data_processed/          # Cleaned, feature-engineered data
│   ├── daily_sentiment.csv
│   └── features_final.csv
│
├── experiments/             # Model runs, logs, results
│   ├── results_logistic.txt
│   ├── results_xgboost.txt
│   └── training_log.csv
│
├── docs/                    # Documentation
│   ├── ASSUMPTIONS.md       # Detailed assumptions (this section)
│   ├── DATA_DICTIONARY.md   # Column definitions
│   ├── MODELING_NOTES.md    # Technical approach & findings
│   └── FUTURE_WORK.md       # Next steps & improvements
│
└── requirements.txt         # Python dependencies
```

---

## Installation & Setup

### Prerequisites
- Python 3.8+
- pip or conda

### Create Virtual Environment
```bash
cd sentiment-analysis-stock-prediction
python -m venv venv
source venv/bin/activate      # On Windows: venv\Scripts\activate
```

### Install Dependencies
```bash
pip install -r requirements.txt
```

### Download Data
```bash
# Raw data is already in data_raw/
# Run notebooks to process and generate data_processed/
```

---

## Quick Start

### 1. Explore Data
```bash
cd notebooks
jupyter notebook filtered_headlines.ipynb
```

### 2. Train Models
```bash
jupyter notebook Models1.ipynb
```

### 3. Use Pipeline (Future)
```bash
python -m src.pipeline --config config.yaml
```

---

## Key Findings (To Be Updated)

- [ ] Which model performs best?
- [ ] What is the baseline accuracy (random/majority class)?
- [ ] Is sentiment predictive of next-day returns?
- [ ] Which news sources contribute most to predictions?
- [ ] Best performing models & hyperparameters

---

## Future Improvements

1. **Feature Engineering**
   - Lagged sentiment (sentiment from previous days)
   - Sentiment from specific news categories
   - Sentiment volatility/consistency

2. **Temporal Methods**
   - LSTM/GRU for sequence modeling
   - Attention mechanisms for article importance weighting
   - Time series cross-validation

3. **Data Expansion**
   - Include market microstructure data (volume, volatility)
   - Add macroeconomic indicators
   - Incorporate social media sentiment (Twitter, Reddit)

4. **Model Improvements**
   - Hyperparameter optimization (GridSearch/Optuna)
   - Ensemble methods combining multiple models
   - Backtesting with realistic trading constraints

5. **Production**
   - Real-time sentiment pipeline
   - Model retraining schedule
   - Performance monitoring & drift detection

---

## Contributors
- Amogh

---

## License
[Specify your license - e.g., MIT, Apache 2.0]

---

## Contact
[Your email or contact info]

---

## References

- **FinBERT Paper:** [FinBERT: Financial Sentiment Analysis with Pre-trained Language Models](https://arxiv.org/abs/1908.04597)
- **Transformers:** [Hugging Face Transformers Library](https://huggingface.co/transformers/)
- **NLTK:** [Natural Language Toolkit Documentation](https://www.nltk.org/)
- **Scikit-learn:** [Scikit-learn Documentation](https://scikit-learn.org/)
- **yfinance:** [yfinance Documentation](https://github.com/ranaroussi/yfinance)

---

**Last Updated:** January 2026  
**Project Status:** Early Development - In Active Development
