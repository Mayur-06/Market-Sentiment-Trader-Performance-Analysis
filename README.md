# Market Sentiment & Trader Performance — Methodology

Analysis of Hyperliquid trading activity against the Bitcoin Fear & Greed Index, exploring how market sentiment relates to trader behavior and performance.

---

## Data

**1. Bitcoin Fear & Greed Index**
| Column | Description |
|---|---|
| `date` | Calendar date (UTC) |
| `value` | Sentiment score, 0–100 |
| `classification` | Extreme Fear / Fear / Neutral / Greed / Extreme Greed |

**2. Hyperliquid Historical Trades**
| Column | Description |
|---|---|
| `account` | Trader wallet address |
| `coin` / `symbol` | Traded asset |
| `execution price`, `size`, `side` | Trade mechanics |
| `closedPnL` | Realized profit/loss |
| `leverage`, `start position` | Risk & position context |
| `time` | Trade timestamp (IST) |

Trade timestamps are in IST; the sentiment index is UTC-daily. This mismatch is resolved during preprocessing so trades are matched to the correct sentiment day rather than shifted by a few hours across the date boundary.

---

## Approach

### 1. Data preparation
- Convert trade timestamps from IST to UTC before joining on date, to avoid mislabeling trades against the wrong sentiment day.
- Aggregate the joined data at four granularities, each answering a different question:
  - **Per trade** — fine-grained pattern mining
  - **Per account-day** — the core unit for sentiment-vs-performance comparisons
  - **Per account (overall)** — trader-level profiling and segmentation
  - **Per coin-day** — sentiment sensitivity by asset

### 2. Feature engineering
- `win` — binary flag from `closedPnL > 0`
- `leverage_bucket` — Low / Medium / High, for clean cross-tabs instead of noisy raw values
- `position_sizing_pct` — standard deviation of trade size relative to an account's own average trade size, used as a proxy for conviction/outsized bets
- `win_rate_fear`, `win_rate_greed`, `fear_greed_win_gap` — per-account win rate split by sentiment regime, used to detect behavioral bias toward Fear or Greed conditions

### 3. Statistical testing
- **Kruskal-Wallis test** on daily PnL across sentiment classes — chosen over ANOVA because PnL is heavily skewed/fat-tailed, and the test doesn't assume normality.
- **Chi-square test** on the leverage-bucket × sentiment-class crosstab, to confirm whether risk-taking patterns across regimes are statistically meaningful rather than incidental.
- **Mean vs. median comparison** on all PnL aggregates — included after the mean was found to be heavily distorted by a small number of outlier accounts; median is reported alongside mean throughout to avoid overstating effects driven by a handful of large trades.

### 4. Lag analysis
- Sentiment shifted by 1, 3, and 7 days relative to trading activity, to test whether sentiment's effect on performance persists or predicts forward rather than only reflecting same-day conditions.
- Sample size and standard deviation checked at each lag before drawing conclusions — some longer-lag patterns were found to be driven by shrinking, outlier-sensitive buckets rather than a genuine persistent effect, and were treated accordingly.

### 5. Behavioral segmentation
- **K-Means clustering** on standardized account-level features: net PnL, position-sizing conviction, and the Fear-vs-Greed win-rate gap.
- Cluster count selected using silhouette score across k=2–6, rather than an arbitrary default.
- Resulting clusters profiled by mean feature values and account count, distinguishing large, generalizable segments from small clusters that represent individual notable accounts rather than a statistically robust population.

---

## Tools

`pandas`, `numpy`, `scikit-learn` (KMeans, StandardScaler, silhouette_score), `scipy.stats` (kruskal, chi2_contingency), `matplotlib`

---

## Notes on rigor

- Every headline comparison is backed by a significance test, not just a visual difference in bar heights.
- Mean-based findings are cross-checked against the median wherever the underlying distribution is suspected (or shown) to be skewed.
- Cluster and lag findings are reported with their sample sizes so that small, outlier-driven segments aren't mistaken for broad population trends.
