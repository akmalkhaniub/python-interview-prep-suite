# Technical Study Guide: Senior Logistics Analyst (foodpanda)

## 1. Logistics Optimization & Operations Research
- **Fleet Sizing:** Predicting the optimal number of riders to balance cost (idle time) vs. service (wait time).
- **Routing Algorithms:** Basic understanding of the Vehicle Routing Problem (VRP) and heuristics.
- **Demand Planning:** Using Time Series and machine learning to forecast order volume at the hexagon/city level.
- **Simulation:** Using Python (e.g., `SimPy`) to model "What-if" scenarios for warehouse or delivery operations.

## 2. Causal Inference (Operational focus)
- **Difference-in-Differences (DiD):** Measuring the change in a metric (e.g., Delivery Time) between a treated group and a control group over time.
- **Synthetic Controls:** Creating a "fake" control group by weighting multiple untreated units to match the pre-treatment trend of a single treated unit.
- **Switchback Experiments:** Toggling a feature (e.g., a new dispatch logic) for an entire city in 30-minute intervals to avoid "Network Interference."
- **Cluster Randomization:** Randomizing groups of hexagons/zones instead of individual orders.

## 3. Google Cloud Platform (GCP) & Big Data
- **BigQuery:**
    - Window Functions for analyzing delivery sequences.
    - Partitioning and Clustering for performance optimization.
    - BigQuery ML for rapid prototyping of logistics models.
- **Vertex AI:** Leveraging AI models for demand forecasting and fleet optimization.

## 4. Agentic AI & Modern Analytics
- **Agentic Workflows:** Using LLMs to generate SQL, summarize reports, or automate root cause detection.
- **Self-Serve Analytics:** Building dashboards that allow domain leads to perform their own "Deep Dives."

## 5. Statistical Modeling in Python
- **Regression:** Modeling delivery duration as a function of weather, traffic, and distance.
- **Optimization Libraries:** Using `SciPy.optimize` or `PuLP` for resource allocation problems.

## 6. Root Cause Analysis (RCA)
- **Decomposition:** Breaking down "Average Delivery Time" into Preparation Time + Wait Time + Travel Time to find the bottleneck.
- **Anomaly Detection:** Identifying when a specific zone is underperforming due to external factors (e.g., road closures).

## Resources to Review
- [Introduction to Causal Inference (CausalML)](https://causalml.readthedocs.io/en/latest/index.html)
- [Switchback Testing at Uber (Engineering Blog)](https://www.uber.com/en-PK/blog/switchback-tests/)
- [SimPy: Discrete Event Simulation for Python](https://simpy.readthedocs.io/)
- [BigQuery: Best Practices for Query Optimization](https://cloud.google.com/bigquery/docs/best-practices-performance)
- [Difference-in-Differences (DiD) Explained](https://en.wikipedia.org/wiki/Difference_in_differences)
