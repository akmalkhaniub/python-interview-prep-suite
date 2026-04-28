# 04: Spark and Transformation

Deep expertise in PySpark and Spark tuning is required for this role.

## 1. PySpark in Fabric Notebooks
*   **Spark Session**: Automatically provided in Fabric notebooks.
*   **DataFrames**: The primary abstraction for data manipulation.
*   **Lazy Evaluation**: Understanding that Spark doesn't execute transformations until an "Action" (like `.count()` or `.write()`) is called.

## 2. Performance Tuning
*   **Shuffling**: The most expensive operation in Spark (moving data across nodes). Minimize it by using appropriate join types (e.g., Broadcast Join for small tables).
*   **Data Skew**: When one partition is much larger than others. Solved by "Salting" the join key.
*   **Caching/Persist**: Using `.cache()` to keep a DataFrame in memory if it's used multiple times in a notebook.

## 3. Reusable Frameworks
*   **Metadata-driven Pipelines**: Instead of hardcoding tables, use a control table (CSV or SQL) that lists sources, targets, and transformation rules.
*   **Custom Libraries**: Packaging common Python functions into `.whl` files and attaching them to the Fabric environment.

## Interview Questions
1.  **"Explain the difference between a Wide transformation and a Narrow transformation."**
    *   *Ans*: Narrow (e.g., `map`, `filter`) stays within the same partition. Wide (e.g., `groupBy`, `join`) requires a shuffle across the network.
2.  **"How would you optimize a Spark job that is failing with OutOfMemory (OOM) errors?"**
    *   *Ans*: Check for data skew, increase executor memory, or use `broadcast` hints for small joins. Also, check if I'm trying to `collect()` a huge dataset to the driver.
3.  **"What is 'Salting' in Spark and why would you use it?"**
    *   *Ans*: Salting adds a random prefix to a join key to distribute skewed data more evenly across partitions, preventing a single task from being the bottleneck.

## Practice Task
*   Write a PySpark snippet that joins a large `Fact_Sales` table with a small `Dim_Store` table using a broadcast join.
