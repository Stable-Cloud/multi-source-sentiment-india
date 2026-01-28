# Quick Start Guide

**Project:** Sentiment Analysis for Stock Market Prediction  
**Status:** Early Development  
**Last Updated:** January 2026  

---

## 🚀 Getting Started (5 minutes)

### 1. Environment Setup
```bash
# Navigate to project
cd "Sentiment Analysis"

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 2. Explore the Data
```bash
# Open exploration notebook
cd notebooks
jupyter notebook filtered_headlines.ipynb
```

### 3. Train Models
```bash
# Open modeling notebook
jupyter notebook Models1.ipynb
```

---

## 📁 Project Structure at a Glance

```
sentiment-analysis-stock-prediction/
├── README.md                          ← Start here for problem overview
├── requirements.txt                   ← Python dependencies
├── .gitignore                        ← Git configuration
│
├── notebooks/                        ← Exploration & experiments
│   ├── filtered_headlines.ipynb      ← Data loading & EDA
│   └── Models1.ipynb                 ← Model training & comparison
│
├── src/                              ← Production code (future)
│   └── [pipeline modules]
│
├── data_raw/                         ← Raw data (git-ignored if large)
│   ├── rss_articles.csv
│   └── rss_articles.parquet
│
├── data_processed/                   ← Cleaned features (for pipeline)
│   └── [processed files]
│
├── experiments/                      ← Model runs & results
│   └── [logs, checkpoints]
│
└── docs/                             ← Documentation
    ├── DATA_DICTIONARY.md            ← Column definitions
    ├── ASSUMPTIONS.md                ← Key assumptions & validation
    ├── MODELING_NOTES.md             ← Technical approach & code
    └── FUTURE_WORK.md                ← Roadmap & next steps
```

---

## 📖 Key Documentation

| Document | Purpose | Read Time |
|----------|---------|-----------|
| **README.md** | Problem, target, assumptions | 10 min |
| **DATA_DICTIONARY.md** | Column definitions | 5 min |
| **ASSUMPTIONS.md** | Key assumptions & risks | 15 min |
| **MODELING_NOTES.md** | Technical implementation | 20 min |
| **FUTURE_WORK.md** | Roadmap & next phases | 15 min |

**Recommended reading order:**
1. README.md (understand the problem)
2. DATA_DICTIONARY.md (understand the data)
3. MODELING_NOTES.md (understand the approach)
4. ASSUMPTIONS.md (understand the risks)
5. FUTURE_WORK.md (see next steps)

---

## 🎯 Problem at a Glance

**Question:** Can financial news sentiment predict next-day NIFTY 50 stock market returns?

**Approach:**
1. Extract news from 11 Indian financial sources
2. Analyze sentiment using FinBERT (financial NLP model)
3. Aggregate daily sentiment scores
4. Build 6 classification models to predict returns
5. Evaluate & compare performance

**Target:** Predict if NIFTY 50 will be UP (+1), DOWN (-1), or FLAT (0) tomorrow

---

## 🔍 Current Status

✅ **Completed:**
- Data collection (442 articles)
- FinBERT sentiment extraction
- 6 models implemented (Logistic Regression, Random Forest, XGBoost, MLP, Gradient Boosting, SVM)
- Project structure & documentation

⏳ **In Progress:**
- Model evaluation & comparison
- Hyperparameter tuning
- Baseline comparison

🔮 **Coming Next:**
- Feature engineering (lagged sentiment, volatility)
- Advanced architectures (LSTM, attention)
- Real-time prediction pipeline
- Backtesting framework

---

## 🛠️ Common Tasks

### Run a Notebook
```bash
cd notebooks
jupyter notebook filtered_headlines.ipynb
```

### Add New Dependencies
```bash
pip install <package_name>
pip freeze > requirements.txt
git add requirements.txt
git commit -m "Add <package_name> dependency"
```

### Check Project Status
```bash
cd ..
git status
git log --oneline
```

### Update Documentation
Edit corresponding file in `/docs/` and commit:
```bash
git add docs/<file>.md
git commit -m "Update: <description>"
```

---

## 📊 Models Implemented

| Model | Type | Status |
|-------|------|--------|
| Logistic Regression | Linear baseline | ✓ Coded |
| Random Forest | Tree ensemble | ✓ Coded |
| XGBoost | Boosted trees | ✓ Coded |
| Neural Network (MLP) | Deep learning | ✓ Coded |
| Gradient Boosting | Sequential ensemble | ✓ Coded |
| SVM | Kernel method | ✓ Coded |

**Next:** Evaluate & compare performance against baseline

---

## 🎓 Key Learnings

- **Sentiment is from FinBERT:** Financial-specific NLP model, not general BERT
- **Daily aggregation:** All articles from a day are averaged to one score
- **1-day prediction:** Today's sentiment → Tomorrow's return
- **6 models tested:** Tree ensemble and boosting methods typically beat linear

---

## ❓ FAQ

**Q: How do I run the models?**  
A: Open `notebooks/Models1.ipynb` and run cells. Each cell trains a different model and prints accuracy.

**Q: Where is my data?**  
A: In `data_raw/rss_articles.csv` (raw articles) and market data is fetched from yfinance.

**Q: Can I modify the code?**  
A: Yes! Edit notebooks or create new ones. Commit major changes to git.

**Q: How do I add new features?**  
A: See MODELING_NOTES.md for feature engineering ideas. Create new column in notebook, then document in DATA_DICTIONARY.md.

**Q: Is the model in production?**  
A: No, this is research/development phase. See FUTURE_WORK.md for deployment roadmap.

---

## 💡 Tips for Success

1. **Start with the notebooks:** They contain the full data flow and model code
2. **Understand the assumptions:** Review ASSUMPTIONS.md before making changes
3. **Document as you go:** Update README/docs when changing code
4. **Git often:** Commit frequently with descriptive messages
5. **Follow the structure:** Keep code organized in src/, notebooks/, docs/

---

## 🤝 Collaboration Guidelines

- **Notebooks:** For exploration, EDA, experiments
- **src/:** For production-ready reusable code (future)
- **experiments/:** For saving model runs, logs, results
- **Git:** Use meaningful commit messages
- **Comments:** Explain "why" not "what"

---

## 📞 Next Steps

1. **Understand the problem:** Read README.md + ASSUMPTIONS.md
2. **Explore the data:** Run filtered_headlines.ipynb
3. **Train models:** Run Models1.ipynb (watch for accuracy scores)
4. **Evaluate:** Compare model performance
5. **Plan next features:** Read FUTURE_WORK.md, decide priorities
6. **Implement:** Add features/models, document changes, commit to git

---

## 📚 Useful References

- **FinBERT:** https://arxiv.org/abs/1908.04597
- **Transformers:** https://huggingface.co/transformers/
- **Scikit-learn:** https://scikit-learn.org/stable/
- **Stock data:** https://github.com/ranaroussi/yfinance
- **Git basics:** https://rogerdudley.com/git-basics/

---

**Questions?** Check the relevant doc in `/docs/` or add to this guide!
