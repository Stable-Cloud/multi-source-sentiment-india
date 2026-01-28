# Modeling Approach & Technical Notes

## Problem Formulation

**Task:** Multi-class sentiment-driven stock price prediction  
**Target:** NIFTY 50 1-day ahead return classification  
**Classes:** {-1: Negative, 0: Neutral, +1: Positive}  
**Features:** Single aggregated daily sentiment score  

---

## Data Pipeline

### Stage 1: Data Collection & Loading
```python
# Load raw articles
df = pd.read_csv("data_raw/rss_articles.csv")
df = df[['timestamp', 'title', 'text_body', 'source']]
df['Date'] = pd.to_datetime(df['timestamp']).dt.date
df = df.dropna(subset=['text_body'])
```

**Output:** 442 articles with 6 columns

### Stage 2: Text Cleaning
```python
# Remove English stopwords, truncate to 512 tokens
def clear_text(text):
    words = str(text).split()
    filtered = [w for w in words if w.lower() not in stopwords]
    return " ".join(filtered)[:512]

df['text_body'] = df['text_body'].apply(clear_text)
```

**Purpose:** Reduce noise, handle token limits

### Stage 3: Sentiment Extraction
```python
# FinBERT sentiment analysis
tokenizer = BertTokenizer.from_pretrained("yiyanghkust/finbert-tone")
model = BertForSequenceClassification.from_pretrained("yiyanghkust/finbert-tone")
finbert = pipeline("sentiment-analysis", model=model, tokenizer=tokenizer)

df['sent_finbert'] = df['text_body'].progress_apply(lambda x: finbert(x)[0])
df['sent_label'] = df['sent_finbert'].apply(lambda x: x['label'].lower())
df['sent_score'] = df['sent_finbert'].apply(lambda x: x['score'])
```

**Output per article:** Label + confidence score

### Stage 4: Sentiment Aggregation
```python
# Map to numeric labels and weight by confidence
sent_map = {"positive": 1, "neutral": 0, "negative": -1}
df['sent_numeric'] = df['sent_label'].map(sent_map)
df['sent_weight'] = df['sent_numeric'] * df['sent_score']

# Daily aggregation
daily = (
    df.groupby('Date')['sent_weight']
    .mean()
    .reset_index()
    .rename(columns={'sent_weight': 'daily_sent'})
)
```

**Output:** Single sentiment score per trading day [-1, 1]

### Stage 5: Market Data Integration
```python
# Fetch NIFTY 50 data
nifty = yf.download("^NSEI", start="2010-01-01")
nifty['Date'] = nifty.index.date

# Merge with sentiment
df_merged = pd.merge(nifty, daily, on="Date", how="left")
df_merged['daily_sent'] = df_merged['daily_sent'].fillna(0)
```

**Output:** Aligned sentiment + market data

### Stage 6: Target Generation
```python
# Calculate 1-day ahead return
df_merged['Return_1d'] = df_merged['Close'].pct_change().shift(-1)

# Classify into 3 classes
def label_signal(r):
    return 1 if r > 0 else (-1 if r < 0 else 0)

df_merged['signal'] = df_merged['Return_1d'].apply(label_signal)
df_merged = df_merged.dropna()
```

**Output:** Classification target aligned with features

---

## Feature Engineering

### Current Features
| Feature | Type | Description |
|---------|------|-------------|
| daily_sent | Float [-1, 1] | Aggregated daily sentiment |

### Future Enhancements
- **Lagged features:** sentiment_t-1, sentiment_t-2
- **Volatility:** std(sentiment) over rolling window
- **Source diversity:** entropy of source distribution
- **Temporal patterns:** day-of-week, seasonal effects
- **Market features:** volume, volatility, technical indicators

---

## Train/Test Split Strategy

```python
X = df_merged[['daily_sent']]
y = df_merged['signal']

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, shuffle=False  # Temporal ordering preserved
)

scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)
```

**Notes:**
- `shuffle=False` maintains time series order (prevents look-ahead bias)
- 80/20 split for training/testing
- Only scaler fitted on training data (no leakage)

---

## Models Implemented

### 1. Logistic Regression
```python
logr = LogisticRegression(max_iter=200)
logr.fit(X_train_scaled, y_train)
```
- **Type:** Linear classifier
- **Hyperparameters:** max_iter=200
- **Use Case:** Baseline model
- **Pros:** Interpretable, fast, stable
- **Cons:** May underfit non-linear patterns

### 2. Random Forest
```python
rf = RandomForestClassifier(n_estimators=300, max_depth=5)
rf.fit(X_train, y_train)
```
- **Type:** Ensemble tree-based
- **Hyperparameters:** 300 trees, max_depth=5 (prevent overfitting)
- **Use Case:** Non-linear patterns with interpretability
- **Pros:** Handles non-linearity, feature importance, robust
- **Cons:** Can overfit with deep trees

### 3. XGBoost
```python
xgb_clf = xgb.XGBClassifier(
    objective="multi:softmax",
    num_class=3,
    n_estimators=300,
    max_depth=4,
    learning_rate=0.05
)
```
- **Type:** Gradient boosted trees (multiclass)
- **Hyperparameters:** 300 estimators, depth=4, lr=0.05
- **Use Case:** Complex patterns, state-of-the-art
- **Pros:** High accuracy, handles imbalance, fast inference
- **Cons:** Prone to overfitting without tuning

### 4. Neural Network (MLP)
```python
mlp = MLPClassifier(hidden_layer_sizes=(64, 32), max_iter=400)
mlp.fit(X_train_scaled, y_train)
```
- **Type:** 2-layer feedforward network
- **Architecture:** Input(1) → Dense(64) → Dense(32) → Output(3)
- **Hyperparameters:** max_iter=400, ReLU activations
- **Use Case:** Learn complex decision boundaries
- **Pros:** Flexible, can approximate any function
- **Cons:** Black box, requires scaling, prone to overfitting

### 5. Gradient Boosting Classifier
```python
gbc = GradientBoostingClassifier()
gbc.fit(X_train, y_train)
```
- **Type:** Sequential ensemble of decision trees
- **Use Case:** Sequential error correction
- **Pros:** Often better than random forest on many problems
- **Cons:** Slower training, more hyperparameters

### 6. Support Vector Machine (SVM)
```python
svm_clf = SVC(kernel="rbf", C=1.0)
svm_clf.fit(X_train_scaled, y_train)
```
- **Type:** Kernel-based classifier
- **Hyperparameters:** RBF kernel, C=1.0 (regularization)
- **Use Case:** Small datasets, clear margin separation
- **Pros:** Handles high-dimensional data, good generalization
- **Cons:** Slower, requires scaling, multiclass may be suboptimal

---

## Evaluation Metrics

```python
from sklearn.metrics import accuracy_score, classification_report

pred = model.predict(X_test_scaled)
print("Accuracy:", accuracy_score(y_test, pred))
print(classification_report(y_test, pred))
```

**Metrics per model:**
- **Accuracy:** Overall % correct
- **Precision:** TP / (TP + FP) per class
- **Recall:** TP / (TP + FN) per class
- **F1-Score:** Harmonic mean of precision/recall
- **Support:** Number of test samples per class

---

## Baseline Comparison

**Baseline Models:**
1. **Random Classifier:** 33% accuracy (random 3-class prediction)
2. **Majority Class:** Predict most common class for all samples
3. **Market Neutral:** Predict signal=0 for all samples

**Success Criteria:**
- Model accuracy > baseline
- Directional accuracy (positive/negative signal) > 50%
- Statistically significant improvement (p-test)

---

## Hyperparameter Tuning Strategy

*To be implemented:*

```python
from sklearn.model_selection import GridSearchCV

param_grid = {
    'n_estimators': [100, 300, 500],
    'max_depth': [3, 5, 7],
    'learning_rate': [0.01, 0.05, 0.1]
}

grid = GridSearchCV(xgb_clf, param_grid, cv=5, scoring='accuracy')
grid.fit(X_train, y_train)
best_params = grid.best_params_
```

---

## Cross-Validation Strategy

**Temporal Cross-Validation** (recommended for time series):
```python
from sklearn.model_selection import TimeSeriesSplit

tscv = TimeSeriesSplit(n_splits=5)
for train_idx, test_idx in tscv.split(X):
    X_train_cv = X.iloc[train_idx]
    X_test_cv = X.iloc[test_idx]
    # Train and evaluate
```

**Benefit:** Prevents look-ahead bias, respects temporal order

---

## Next Steps & Future Work

1. **Hyperparameter Optimization**
   - Use GridSearchCV or Optuna for systematic tuning
   - Target: 5-10% improvement in accuracy

2. **Feature Engineering**
   - Add lagged sentiment, moving averages
   - Include source-specific sentiment
   - Add market microstructure (volume, volatility)

3. **Data Expansion**
   - Increase historical articles (more sources, longer period)
   - Validate sentiment on additional assets (stocks, commodities)

4. **Advanced Architectures**
   - LSTM for time series
   - Attention mechanisms
   - Ensemble stacking

5. **Validation**
   - Backtesting with transaction costs
   - Walk-forward validation
   - Stress testing on market regime changes

6. **Deployment**
   - Real-time prediction pipeline
   - Model monitoring & retraining
   - Trading signal integration
