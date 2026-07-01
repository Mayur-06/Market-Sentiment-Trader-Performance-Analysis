# 📊 Market Sentiment & Trader Performance

**Do Bitcoin Fear/Greed cycles actually predict how traders perform?**
An analysis of ~130K+ Hyperliquid trades against the Bitcoin Fear & Greed Index — uncovering behavioral patterns, risk-taking cycles, and trader archetypes hidden beneath the headline numbers.

![Mean Daily PnL by Sentiment](assets/mean_pnl_by_sentiment.png)

---

## 🔍 TL;DR — Key Findings

| # | Finding | Evidence |
|---|---|---|
| 1 | Average PnL is highest in **Extreme Fear**, lowest in **Neutral/Greed** | Kruskal-Wallis p = 0.00035 |
| 2 | Win rate and PnL **diverge** — Extreme Greed wins more often, Extreme Fear wins bigger | Win rate: 38.8% vs 36.7% |
| 3 | High leverage peaks in **ordinary Greed** (25.3%), not Extreme Greed (19.1%) | Chi² p ≈ 1.1×10⁻¹⁹² |
| 4 | **Mean ≠ Median** — a handful of whale accounts drive the "Fear wins" story; the typical trader does slightly better in Greed | Median lag-1: Extreme Greed $354 vs Extreme Fear $262 |
| 5 | Clustering reveals distinct trader archetypes — "Fear Whales," "Greed Specialists," and a large balanced core population | k=5, silhouette 0.230 |

📄 **[Read the full report →](Sentiment_Trader_Performance_Report.docx)**

---

## 🗂️ Datasets

**1. Bitcoin Market Sentiment (Fear & Greed Index)**
| Column | Description |
|---|---|
| `date` | Calendar date (UTC) |
| `value` | Sentiment score, 0–100 |
| `classification` | Extreme Fear / Fear / Neutral / Greed / Extreme Greed |

**2. Historical Trader Data (Hyperliquid)**
| Column | Description |
|---|---|
| `account` | Trader wallet address |
| `symbol` / `coin` | Traded asset |
| `execution price`, `size`, `side` | Trade mechanics |
| `closedPnL` | Realized profit/loss |
| `leverage`, `start position` | Risk & position context |
| `time` | Trade timestamp (IST) |

> ⚠️ **Timezone note:** trade timestamps are IST; sentiment is UTC-daily. Both are reconciled during preprocessing to avoid mislabeling trades against the wrong sentiment day.

---

## 🧠 Methodology

1. **Data prep** — timezone-aligned join, aggregation to trade / account-day / account-overall / coin-day levels
2. **Feature engineering** — win/loss flag, leverage buckets, position-sizing conviction (std dev of trade size vs. account baseline), Fear-vs-Greed win-rate gap
3. **Statistical testing** — Kruskal-Wallis (PnL across sentiment classes), Chi-square (leverage vs. sentiment association)
4. **Lag analysis** — sentiment shifted 1/3/7 days to test predictive persistence, validated with mean **and** median to control for fat-tailed outliers
5. **Behavioral segmentation** — K-Means clustering (k=5, standardized features) on PnL, conviction, and sentiment-driven win-rate bias

---

## 📈 Visual Highlights

<table>
<tr>
<td><img src="assets/win_rate_by_sentiment.png" width="400"/></td>
<td><img src="assets/leverage_by_sentiment.png" width="400"/></td>
</tr>
<tr>
<td align="center"><i>Win rate vs. PnL diverge across regimes</i></td>
<td align="center"><i>Leverage risk peaks in ordinary Greed</i></td>
</tr>
<tr>
<td><img src="assets/mean_vs_median_lag1.png" width="400"/></td>
<td><img src="assets/scatter_archetypes.png" width="400"/></td>
</tr>
<tr>
<td align="center"><i>Mean vs. median exposes whale-driven skew</i></td>
<td align="center"><i>Trader archetypes: Fear vs. Greed win rate</i></td>
</tr>
</table>

---

## 🧩 Trader Archetypes

| Cluster | Accounts | Avg Net PnL | Fear–Greed Gap | Archetype |
|---|---|---|---|---|
| 3 | 2 | $1.86M | +0.134 | **Fear Whales** |
| 0 | 2 | $654K | −0.083 | Greed-Leaning Elite |
| 1 | 8 | $210K | +0.043 | Mild Fear Tilt, High Conviction |
| 2 | 15 | $177K | +0.068 | **Core Population** (Balanced/Fear-Leaning) |
| 4 | 5 | $137K | −0.327 | Greed Specialists |

> Clusters 0, 3, and 4 are small (n=2–5) and best read as case studies rather than statistically robust segments.

---

## 📁 Repo Structure

```
├── data/
│   ├── fear_greed_index.csv
│   └── hyperliquid_trades.csv
├── notebooks/
│   └── sentiment_trader_analysis.ipynb
├── assets/                          # charts used in this README / report
├── Sentiment_Trader_Performance_Report.docx
└── README.md
```

---

## ⚙️ Setup & Reproduction

```bash
git clone https://github.com/<your-username>/<repo-name>.git
cd <repo-name>
pip install -r requirements.txt
jupyter notebook notebooks/sentiment_trader_analysis.ipynb
```

**Core dependencies:** `pandas`, `numpy`, `scikit-learn`, `scipy`, `matplotlib`, `seaborn`

---

## ⚠️ Limitations

- Small cluster sizes (n=2–5) for the most distinctive segments limit statistical generalizability
- PnL is heavily fat-tailed — mean-based comparisons are reported alongside medians throughout
- Lag effects beyond 1 day are less stable due to shifting outlier composition
- Single historical window — regime relationships may not generalize to other market cycles

---

## 💡 Actionable Takeaways

- **Risk monitoring:** ordinary Greed, not Extreme Greed, is the highest-risk leverage window
- **Strategy design:** high-conviction sizing during Fear regimes shows a real, persistent edge for top accounts
- **Evaluation:** benchmark "typical" trader performance with median PnL, not mean

---

## 📜 License

MIT — feel free to fork, extend, or build on this analysis.
