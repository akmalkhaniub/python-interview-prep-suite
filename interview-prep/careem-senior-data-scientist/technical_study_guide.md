# Technical Study Guide: Senior Data Scientist (Careem)

## 1. Marketplace Science
- **Matching & Dispatch:** Stable Marriage problem, Hungarian algorithm, and Batch matching for ride-hailing.
- **Dynamic Pricing:** Surge pricing logic based on supply/demand elasticity.
- **Supply Management:** Predicting "Captain" churn and optimizing availability through incentives.

## 2. Big Data Mastery (Spark/Hive)
- **PySpark:** DataFrames, RDDs, and Spark SQL optimization (partitioning, bucketing).
- **Hive:** Managing large-scale data warehouses and optimizing complex JOINs.
- **BigQuery:** Leveraging serverless analytics for high-speed exploratory analysis.

## 3. Uplift Modeling & Recommendations
- **Uplift Modeling:** Two-model approach vs. Class-transformation method.
- **Learning to Rank:** Pairwise, Pointwise, and Listwise approaches (XGBoost Ranker, LightGBM).
- **Recommendations:** Collaborative Filtering, Content-based filtering, and Hybrid systems for the "Everything App."

## 4. Experimentation (AB Testing)
- **Foundations:** P-values, Power, Sample size calculation.
- **Marketplace Specifics:** Handling "Network Effects" (interference) using Cluster-based randomization.
- **Switchback Testing:** A common strategy for ride-hailing where treatments are toggled over time periods in specific regions.

## 5. Advanced Machine Learning
- **Time Series:** LSTMs, Prophet, and ARIMA for demand forecasting.
- **Reinforcement Learning:** Multi-armed Bandits (MAB) for real-time incentive optimization.
- **Deep Learning:** Embeddings for user behavior modeling.

## 6. Geospatial Data Processing
- **H3 (Uber's Indexing):** Using hexagonal grids for spatial analysis and aggregation.
- **Geo-fencing:** Real-time location tracking and boundary analysis.

## Resources to Review
- [Marketplace Engineering (Uber Blog)](https://www.uber.com/en-PK/blog/marketplace/)
- [Uplift Modeling with Python (CausalML)](https://causalml.readthedocs.io/en/latest/index.html)
- [Spark: The Definitive Guide (O'Reilly)](https://www.oreilly.com/library/view/spark-the-definitive/9781491912201/)
- [Experimentation at Airbnb (Medium)](https://medium.com/airbnb-engineering/experimentation-at-airbnb-640f8983936a)
- [Learning to Rank with LightGBM](https://lightgbm.readthedocs.io/en/latest/index.html)
