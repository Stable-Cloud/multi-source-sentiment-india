# Key Assumptions & Validation

## Critical Assumptions

### 1. Sentiment Analysis Accuracy
**Assumption:** FinBERT accurately classifies financial news sentiment.

**Validation:**
- [ ] Compare FinBERT predictions against manual annotated samples
- [ ] Evaluate performance on financial vs. general news
- [ ] Assess handling of financial jargon and domain-specific language

**Risk:** If FinBERT is inaccurate, all downstream predictions are unreliable.

---

### 2. Temporal Alignment
**Assumption:** News sentiment on date T predicts market return on date T+1.

**Validation:**
- [ ] Verify no look-ahead bias (articles published after market close)
- [ ] Check handling of pre-market vs. intraday news
- [ ] Confirm market hours alignment across sources

**Risk:** Misalignment could create spurious correlations.

---

### 3. Sample Representativeness
**Assumption:** RSS articles represent the market's information set and sentiment.

**Validation:**
- [ ] Compare source distribution to broader financial news ecosystem
- [ ] Check for selection bias (e.g., sensational headlines over-represented)
- [ ] Assess temporal consistency (no gaps in data collection)

**Risk:** Biased sample could lead to non-generalizable models.

---

### 4. Stationarity & Stability
**Assumption:** Sentiment-return relationship is stable over time.

**Validation:**
- [ ] Test for structural breaks in the data
- [ ] Compare model performance across different market regimes
- [ ] Evaluate rolling window correlations

**Risk:** If relationship changes, model becomes obsolete.

---

### 5. Causal Direction
**Assumption:** Sentiment → Returns (not the reverse).

**Validation:**
- [ ] Analyze Granger causality
- [ ] Test lead-lag relationships
- [ ] Look for reverse causality (negative returns → negative sentiment)

**Risk:** Reverse causality or simultaneity bias invalidates predictions.

---

## Data Quality Assumptions

### Missing Values
- **Assumption:** Missing author/url don't impact sentiment analysis
- **Validation:** Confirm these are optional fields
- **Risk:** If text_body is missing, that article is dropped

### Duplicates
- **Assumption:** No duplicate articles in the dataset
- **Validation:** [ ] Check for identical titles or text hashes
- **Risk:** Duplicates overweight certain events

### Text Preprocessing
- **Assumption:** Stopword removal and 512-token limit preserve sentiment
- **Validation:** [ ] A/B test with/without preprocessing
- **Risk:** Loss of important words could degrade sentiment

---

## Statistical Assumptions

### Feature Distribution
- **Assumption:** Features are approximately normal after scaling
- **Validation:** [ ] QQ-plots, Shapiro-Wilk test
- **Risk:** Violated assumptions in linear models (logistic regression)

### Multicollinearity
- **Assumption:** Features (daily sentiment) are not highly correlated with each other
- **Validation:** [ ] VIF, correlation matrix
- **Risk:** Inflated coefficients, unstable predictions

### Class Balance
- **Assumption:** Classes are reasonably balanced or models account for imbalance
- **Validation:** [ ] Check class distribution in signal
- **Risk:** Models may be biased toward majority class

---

## Market Assumptions

### Efficiency
- **Assumption:** Markets are semi-efficient; some sentiment value exists
- **Reality:** If markets were perfectly efficient, sentiment would have zero predictive power
- **Risk:** If true efficiency, this entire project fails

### Holding Period
- **Assumption:** 1-day holding period captures sentiment value
- **Reality:** Sentiment might take longer to manifest
- **Alternative:** [ ] Test 2-day, 3-day, 5-day forecasts

### Index Representation
- **Assumption:** NIFTY 50 sentiment matches broader market or individual stocks
- **Risk:** Sentiment might only predict specific sectors/stocks

---

## Validation Checklist

- [ ] Check for look-ahead bias before finalizing training data
- [ ] Validate temporal continuity in data collection
- [ ] Compare model predictions with baseline (random, buy-and-hold)
- [ ] Test on recent out-of-sample data
- [ ] Verify no leakage from future information
- [ ] Examine model predictions for sanity (do they make business sense?)
- [ ] Check for overfitting (cross-validation scores)

---

## Known Limitations

1. **Small Dataset:** 442 articles may not capture full market sentiment
2. **Survivorship Bias:** RSS feeds only available for existing sources
3. **Publication Bias:** Only published news available (not private info)
4. **English Only:** Non-English sources and news excluded
5. **Single Country:** India-focused; may not generalize to other markets
6. **Single Asset Class:** Only equity index; fixed income/FX excluded

---

## Assumptions to Revisit

If model performance is poor, investigate these in order:
1. Data quality (duplicates, missing values, errors)
2. Temporal alignment (look-ahead bias, timing)
3. Sentiment accuracy (FinBERT validation)
4. Feature engineering (better aggregation methods)
5. Model selection (try different architectures)
6. Assumption validity (test for causality, stationarity)
