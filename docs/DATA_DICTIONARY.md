# Data Dictionary

## Raw Data: rss_articles.csv

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| source | String | News outlet/publisher | "Economic Times", "Moneycontrol", "Mint" |
| timestamp | Datetime | Article publication datetime | "2025-12-28 10:30:00" |
| title | String | Article headline | "Markets close higher on positive sentiment" |
| text_body | String | Article content (summary or full text) | "The market rally continued..." |
| author | String | Article author name | "John Doe" |
| url | String | URL to original article | "https://economictimes.indiatimes.com/..." |

**Data Shape:** 442 articles across multiple sources  
**Date Range:** Historical (exact range TBD)  
**Missing Values:** Some entries may have null author/url

---

## Processed Data: daily_sentiment.csv

| Column | Type | Description | Range |
|--------|------|-------------|-------|
| Date | Date | Trading date (YYYY-MM-DD) | 2008-2025 |
| daily_sent | Float | Aggregated daily sentiment | -1.0 to 1.0 |

**Calculation:**
```
daily_sent = mean(sent_numeric × sent_score) for all articles on Date
```
- `sent_numeric`: -1 (negative), 0 (neutral), +1 (positive)
- `sent_score`: FinBERT confidence score (0 to 1)
- Result is weighted sentiment intensity

---

## Market Data: NIFTY 50 Daily

| Column | Type | Description | Source |
|--------|------|-------------|--------|
| Date | Date | Trading date | yfinance |
| Close | Float | Daily closing price | ^NSEI |

---

## Merged Training Data: df_merged

| Column | Type | Description | Derivation |
|--------|------|-------------|------------|
| Date | Date | Trading date | From nifty data |
| Close | Float | NIFTY 50 closing price | From yfinance |
| daily_sent | Float | Daily aggregated sentiment | From RSA articles |
| Return_1d | Float | Next-day percentage return | (Close[t+1] - Close[t]) / Close[t] |
| signal | Int | Target classification | -1, 0, or 1 based on Return_1d |

---

## Text Processing Notes

- **Stopword Removal:** English stopwords (NLTK corpus)
- **Token Limit:** Text truncated to 512 tokens (FinBERT limit)
- **No Stemming/Lemmatization:** FinBERT handles this internally

---

## FinBERT Sentiment Output

| Label | Meaning | Numeric | Description |
|-------|---------|---------|-------------|
| positive | Bullish/Positive | +1 | Expected price increase |
| neutral | Neutral/Mixed | 0 | No clear direction |
| negative | Bearish/Negative | -1 | Expected price decrease |

**Score:** Confidence (0-1), higher = more confident
**Weighted Score:** `numeric_label × score` ranges from -1 to +1

---

## Sources Included

- Economic Times (`df_ecotime`)
- Economic Times - Market updates (`df_ecomarket`)
- Economic Times - Stock updates (`df_ecostock`)
- Economic Times - IPOs (`df_ecoipos`)
- Economic Times - Expert analysis (`df_ecoexpert`)
- Economic Times - Bonds (`df_ecobonds`)
- Moneycontrol main (`df_moneycontrol`)
- Mint - Money section (`df_mintmoney`)
- Mint - Market section (`df_mintmarket`)
- Times of India - Business (`df_timesindia`)
- Hindustan Times - Business (`df_hindustan`)

**Total Articles:** 442
