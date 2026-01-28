# Future Work & Roadmap

## Phase 2: Advanced Feature Engineering

### Temporal Features
- [ ] Lagged sentiment: `daily_sent_t-1`, `daily_sent_t-2`
- [ ] Rolling sentiment: 5-day, 10-day moving average
- [ ] Sentiment momentum: `daily_sent_t - daily_sent_t-5`
- [ ] Sentiment volatility: std of rolling window

### Source-Level Features
- [ ] Per-source sentiment tracking
- [ ] Source reliability weights
- [ ] Source diversity score (entropy)
- [ ] Positive/negative article ratio per source

### Market Features
- [ ] Previous day return: `Return_1d_t-1`
- [ ] Volatility: 20-day rolling std
- [ ] Volume changes
- [ ] Price momentum indicators (RSI, MACD)

### Text-Based Features
- [ ] Average article length per day
- [ ] Number of articles per day
- [ ] Headline sentiment vs. body sentiment
- [ ] Uncertainty language detection

---

## Phase 3: Advanced Modeling

### Deep Learning
```python
# LSTM for sentiment sequences
class SentimentLSTM(nn.Module):
    def __init__(self, input_size=1, hidden_size=64, num_layers=2, num_classes=3):
        super().__init__()
        self.lstm = nn.LSTM(input_size, hidden_size, num_layers, batch_first=True)
        self.fc = nn.Linear(hidden_size, num_classes)
    
    def forward(self, x):
        lstm_out, _ = self.lstm(x)
        return self.fc(lstm_out[:, -1, :])
```

### Transformer-Based Models
- [ ] Attention mechanisms for article importance
- [ ] Cross-attention between sentiment and price
- [ ] Temporal self-attention

### Ensemble Methods
- [ ] Stacking: meta-learner on model predictions
- [ ] Blending: weighted average of diverse models
- [ ] Bagging variants of different base learners

---

## Phase 4: Data Expansion

### Additional Data Sources
- [ ] Social media sentiment (Twitter/X, Reddit, StockTwits)
- [ ] Earnings call transcripts
- [ ] Analyst reports
- [ ] Macroeconomic news
- [ ] Competitor/sector news

### Asset Coverage
- [ ] Individual stocks (top 50 by market cap)
- [ ] Sector indices (IT, Finance, Auto, etc.)
- [ ] Broader market indices (BSE Sensex)
- [ ] Fixed income (bonds, G-secs)
- [ ] Commodities (gold, oil)

### Time Period Extension
- [ ] Extend historical data 5+ years back
- [ ] Real-time prediction capability
- [ ] Forecast multiple horizons (2-day, 1-week, 1-month)

---

## Phase 5: Validation & Backtesting

### Statistical Tests
- [ ] Granger causality (sentiment → returns)
- [ ] Vector AutoRegression (VAR) models
- [ ] Structural break tests (Chow test)
- [ ] Rolling window correlations

### Backtesting Framework
```python
class BacktestEngine:
    def __init__(self, model, transaction_cost=0.001):
        self.model = model
        self.transaction_cost = transaction_cost
    
    def backtest(self, X, y, initial_capital=100000):
        """
        Simulate trading strategy based on predictions
        Returns: Cumulative P&L, Sharpe ratio, max drawdown
        """
        positions = self.model.predict(X)
        returns = calculate_returns(positions, y, self.transaction_cost)
        return performance_metrics(returns, initial_capital)
```

**Metrics to Calculate:**
- [ ] Cumulative return
- [ ] Sharpe ratio
- [ ] Sortino ratio
- [ ] Maximum drawdown
- [ ] Win rate (% profitable trades)
- [ ] Profit factor (wins/losses)

### Walk-Forward Validation
```python
# Expanding window: train on [0, t], test on [t, t+k]
# Then expand to train on [0, t+k], test on [t+k, t+2k]
for t in range(min_train, len(data)-test_size):
    train_data = data[:t]
    test_data = data[t:t+test_size]
    # Train and evaluate
```

---

## Phase 6: Production Deployment

### Real-Time Pipeline
```
RSS Feed Aggregator
    ↓
Text Preprocessing
    ↓
FinBERT Sentiment
    ↓
Daily Aggregation
    ↓
Feature Engineering
    ↓
Model Inference
    ↓
Signal Generation
    ↓
[Trading System / Dashboard]
```

### Model Monitoring
- [ ] Performance drift detection
- [ ] Data quality checks
- [ ] Prediction confidence monitoring
- [ ] Retraining schedule (weekly/monthly)

### Deployment Checklist
- [ ] Unit tests for all modules
- [ ] Integration tests for pipeline
- [ ] Load testing (can it handle real-time data?)
- [ ] Logging & error handling
- [ ] Model versioning & rollback capability
- [ ] API for serving predictions
- [ ] Dashboard for monitoring

---

## Phase 7: Advanced Techniques

### Transfer Learning
- [ ] Fine-tune FinBERT on custom financial corpus
- [ ] Use pre-trained models as feature extractors
- [ ] Domain adaptation for new markets/assets

### Active Learning
- [ ] Identify most uncertain predictions
- [ ] Request manual labeling for those samples
- [ ] Retrain with expanded labeled dataset

### Few-Shot Learning
- [ ] Predict on new assets with limited data
- [ ] Meta-learning approaches
- [ ] Prototypical networks

### Causal Inference
- [ ] Causal forests to identify sentiment impact
- [ ] Instrumental variables (IV) regression
- [ ] Propensity score matching

---

## Phase 8: Optimization

### Computational Efficiency
- [ ] Model quantization (int8/fp16)
- [ ] Knowledge distillation
- [ ] Pruning unnecessary weights
- [ ] Target: 10x faster inference

### Hardware Scaling
- [ ] GPU acceleration for inference
- [ ] Distributed training for large datasets
- [ ] Edge deployment (mobile/IoT)

---

## Success Metrics

- [ ] **Accuracy:** > 55% (vs. 33% baseline)
- [ ] **Directional Accuracy:** > 52% (is prediction direction correct?)
- [ ] **Information Coefficient:** > 0.05 (correlation with actual returns)
- [ ] **Sharpe Ratio (backtest):** > 1.0
- [ ] **Max Drawdown:** < 20%
- [ ] **Inference Latency:** < 100ms
- [ ] **Model Size:** < 500MB

---

## Timeline (Estimated)

| Phase | Timeline | Effort |
|-------|----------|--------|
| Phase 1 (Current) | Week 1 | 40% |
| Phase 2 | Weeks 2-3 | 30% |
| Phase 3 | Weeks 4-5 | 40% |
| Phase 4 | Weeks 6-8 | 20% |
| Phase 5 | Weeks 9-10 | 25% |
| Phase 6 | Weeks 11-12 | 30% |
| Phase 7 | Ongoing | Variable |
| Phase 8 | Ongoing | Variable |

---

## Research Questions to Address

1. **Is sentiment predictive?**
   - What's the information coefficient?
   - Does it work across market regimes?

2. **Which source matters most?**
   - Do different sources have different predictive power?
   - Should we weight sources differently?

3. **What time horizons work?**
   - 1-day is our current focus
   - What about intraday? Week-ahead? Month-ahead?

4. **How to handle imbalanced classes?**
   - Oversampling, undersampling, or SMOTE?
   - Class weights in loss function?

5. **Can we predict volatility instead of direction?**
   - Alternative problem: predict high/low volatility days

6. **How to incorporate real-time updates?**
   - Should we update sentiment estimates during market hours?

---

## Dependencies & Tools Needed

### New Libraries
- `torch`, `pytorch-lightning` (deep learning)
- `optuna` (hyperparameter optimization)
- `shap` (model explainability)
- `mlflow` (experiment tracking)
- `fastapi` (REST API)
- `celery` (task scheduling)
- `postgres` (data storage)

### Infrastructure
- [ ] Cloud platform (AWS/GCP/Azure)
- [ ] Containerization (Docker)
- [ ] Orchestration (Kubernetes/Airflow)
- [ ] Monitoring (Prometheus/Grafana)

---

## Notes for Next Session

- [ ] Verify current model performance benchmarks
- [ ] Document any lessons learned from initial development
- [ ] Identify bottlenecks (data, compute, modeling)
- [ ] Prioritize which phases to tackle first based on business impact
