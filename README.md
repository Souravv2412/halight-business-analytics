# Halight Performance Intelligence

Live performance dashboard system for Halight — pulling data from AWS Athena, caching locally, and serving a real-time dashboard.

---

## Quick Start

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Add your AWS credentials to .env
# (copy .env and fill in your keys)

# 3. Test your connection
cd notebooks && jupyter notebook 00_test_connection.ipynb

# 4. Run user analysis (data fetches ONCE, then loads from cache)
jupyter notebook 01_user_analysis.ipynb
```

---

## How Data Loading Works

```
First run  → Queries Athena → Saves to data/raw/users.parquet
Every run after → Loads from local parquet instantly (no AWS wait)
Force fresh pull → fetch_users(force_refresh=True)
```

---

## Folder Structure

```
Halight-Performance-Intelligence/
│
├── .env                        # AWS credentials (never commit)
├── .gitignore
├── requirements.txt
├── README.md
│
├── config/
│   └── settings.py             # All paths + env vars loaded here
│
├── data/
│   ├── raw/                    # Athena pulls cached as .parquet (gitignored)
│   ├── staging/                # Cleaned individual tables
│   ├── marts/                  # Joined analytical datasets
│   ├── metrics/                # Dashboard-ready JSON/parquet
│   └── models/                 # Saved ML models
│
├── notebooks/
│   ├── 00_test_connection.ipynb
│   └── 01_user_analysis.ipynb  # Full EDA — loads via import, no AWS wait
│
├── src/
│   ├── aws/
│   │   ├── athena_client.py    # Core: fetch once, cache forever
│   │   └── fetch_tables.py     # One function per table
│   ├── staging/
│   │   └── users_clean.py      # Cleaning logic (mirrors notebook)
│   ├── marts/                  # Feature mart builders
│   ├── models/                 # ML model scripts
│   ├── llm/                    # LLM insight generation
│   ├── pipeline/               # Automated daily/weekly pipeline
│   └── utils/
│       └── helpers.py          # save/load helpers
│
├── dashboard/
│   ├── app.py                  # Streamlit dashboard entry point
│   ├── static/
│   │   ├── css/
│   │   └── js/
│   ├── templates/
│   └── components/
│
└── Documents/                  # Strategy docs
```

---

## Import Pattern (use in any notebook)

```python
import sys, os
sys.path.insert(0, os.path.abspath('..'))   # point to project root

from src.aws.fetch_tables import fetch_users, fetch_attempts, fetch_rewards
from src.utils.helpers import save_clean_data, load_clean_data

users = fetch_users()     # instant if cached, Athena if not
```

---

## Notebooks Planned

| Notebook | Purpose |
|---|---|
| 00_test_connection | Verify AWS credentials |
| 01_user_analysis | User EDA — demographics, engagement, churn signals |
| 02_engagement_analysis | Learning attempts, scores, completion rates |
| 03_retention_analysis | Cohort retention, churn, lifetime stats |
| 04_feature_mart | Build user_feature_mart for ML |
| 05_modeling | Churn prediction, engagement segmentation |
