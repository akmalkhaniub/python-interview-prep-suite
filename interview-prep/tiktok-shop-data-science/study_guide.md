# Technical Study Guide: Data Scientist (TikTok Shop)

## 1. E-commerce & User Growth Analytics

### Conversion Funnels & Metrics
- **North Star Metric:** GMV (Gross Merchandise Volume) vs. Active Buyers.
- **Conversion Rate (CR):** Breakdown by traffic source (Live, Short Video, Search).
- **Retention:** User LTV (Lifetime Value) and Cohort Analysis.

### A/B Testing in Content Commerce
- **Power & Sample Size:** Calculating required traffic for significant results.
- **Novelty & Primacy Effects:** How to identify if a feature's success is just due to its "newness."
- **Network Effects:** Handling interference when users in the control group are influenced by users in the treatment group (common in social/video apps).

## 2. Risk Control & Fraud Detection

### Abnormal Behavior Detection
- **Scalper/Bot Detection:** Using clustering (K-Means) or anomaly detection (Isolation Forest) on login and purchase patterns.
- **Promotion Abuse:** Identifying "sybil attacks" where one user creates multiple accounts to claim benefits.

### Rule-based vs. Model-based Risk Control
- **Decision Trees:** Often used for explainable risk rules.
- **Graph Neural Networks (GNNs):** Detecting fraud rings by analyzing relationships between accounts, IPs, and devices.

## 3. Machine Learning for User Experience

### Recommendation & Intent Prediction
- **Propensity Modeling:** Predicting the probability of a user purchasing a product after watching a specific video.
- **Uplift Modeling:** Identifying "Persuadables"—users who only buy because of a promotion (optimizing ROI).

### ROI Optimization
- **Causal Inference:** Using Double Machine Learning or Synthetic Control to measure the true incremental impact of marketing spend.

## 4. Big Data Engineering (The TikTok Stack)

### Hadoop & Spark
- **Spark SQL:** Writing efficient queries for massive datasets.
- **Data Skew:** Handling cases where a few popular creators/products account for most of the traffic.

### Hive & Data Modeling
- **Star Schema vs. Snowflake Schema:** Organizing e-commerce data for efficient querying.
- **Partitioning:** Optimizing queries by partitioning data by `date` and `region`.

## 5. Potential Interview Questions

### Technical (Growth & Stats)
1. "How would you measure the impact of a creator's live stream on long-term user retention?"
2. "Design an A/B test for a new 'One-click Buy' button in the comment section."
3. "Explain the 'P-value' to a product manager and why we can't just stop an experiment when it looks 'good'."
4. "How do you handle missing data in user behavior logs?"

### Business & Scenario
1. "TikTok Shop is launching in a new country. What data would you look at to determine the optimal subsidy strategy?"
2. "A media report says user experience is declining. How do you quantify this using platform data?"
3. "How would you use video engagement data (watch time, likes) to improve e-commerce conversion?"
