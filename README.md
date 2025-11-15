#README.md — College Basketball Value Betting Engine
🏀 Overview

This project builds a College Basketball (NCAA Men’s Division I) Value Betting Engine that identifies daily betting lines with the highest expected edge.
The system predicts game margins using advanced efficiency metrics, rolling team stats, and historical performance, then compares model projections to sportsbook odds to detect hidden value.

The main outputs are:

Projected spread

Probability of covering

Value edge vs. sportsbook line

Ranked list of best bets for the day

🧱 Architecture Summary
1. Data Ingestion (ETL)

The system collects and stores:

Source	Data Type	Notes
Torvik API	Team efficiency metrics, game data	Free alternative to KenPom
Sports-Reference	Historical results, box scores	Scraped using Python
ESPN JSON API	Daily schedule	For matchup generation
Odds API / VegasInsider	Betting lines (spread, total, ML)	Free tier supported

All data is stored in a PostgreSQL database inside well-structured relational tables.

2. Feature Engineering

A daily job generates features for each upcoming matchup:

Rolling ORtg/DRtg per team

Offensive/defensive efficiency diffs

Tempo differences

Home/away/neutral flags

Rest days

Conference game flags

Previous spread/total closing lines

Recent form (last N games)

These features populate the features_daily table for model inference.

3. Modeling

The core model predicts final margin (home_score − away_score).

Models supported:

Ridge Regression (baseline)

XGBoost Regression (advanced)

Bayesian margin modeling (optional)

The model outputs:

expected margin

standard deviation (σ)

margin distribution

From these, probabilities are calculated:

cover probability

win probability

expected value

4. Value Detection Engine

For each game and line:

Compute probability of covering:

𝑃
(
margin
>
−
spread
)
P(margin>−spread)

Compare to sportsbook break-even probability (52.38% for -110).

Compute:

Edge
=
𝑃
(
cover
)
−
𝑃
break-even
Edge=P(cover)−P
break-even
	​


Any game with edge > threshold is flagged as a value bet.

5. Daily Output

The engine produces a ranked list of betting edges:

Game: Duke vs UNC
Model Spread: Duke -4.2
Book Spread: Duke -1.5
Cover Prob: 57.5%
Edge: +5.2%
Recommended: Duke -1.5


Export formats:

CSV

JSON

Streamlit dashboard (optional)

Web API endpoint (FastAPI)

cbb-value-engine/
│
├── data_ingestion/
│   ├── torvik_fetch.py
│   ├── sportsref_scrape.py
│   ├── odds_fetch.py
│   └── espn_schedule.py
│
├── database/
│   ├── schema.sql
│   ├── create_db.py
│   └── load_initial_data.py
│
├── features/
│   ├── build_features.py
│   └── rolling_metrics.py
│
├── models/
│   ├── train_margin_model.py
│   ├── inference.py
│   └── utils/
│
├── value_engine/
│   ├── compute_edge.py
│   └── daily_ranking.py
│
├── dashboard/
│   └── app.py
│
├── config/
│   └── settings.yaml
│
├── requirements.txt
└── README.md
