# 06: DevOps and Cost Optimization

Implementing CI/CD and managing Fabric capacities effectively.

## 1. CI/CD in Fabric
*   **Git Integration**: Connecting a Fabric workspace to an Azure DevOps or GitHub repository. Allows for branching and version control of notebooks, pipelines, and report definitions.
*   **Deployment Pipelines**: Moving content from Dev -> Test -> Prod environments within Fabric.
*   **Deployment Rules**: Overriding parameters (e.g., connection strings) as items move between stages.

## 2. Capacity & Cost Optimization
*   **Workload Isolation**: Using different capacities or workspaces for ETL vs. Reporting to prevent resource contention.
*   **Smoothing**: Understanding that if you over-consume in one hour, Fabric will throttle you in the next hours unless you have enough "overhead" in your capacity.
*   **Compute Selection**: Knowing when to use Spark (Lakehouse) vs. SQL (Warehouse) based on the cost/performance profile of the task.

## 3. Monitoring & Observability
*   **Fabric Capacity Metrics App**: The primary tool to monitor CU usage and identify "expensive" items.
*   **Query Insights**: Analyzing SQL performance in the Warehouse.
*   **Spark Advisor**: Getting automated tips on how to improve notebook performance.

## Interview Questions
1.  **"How would you set up a CI/CD pipeline for a Fabric Notebook?"**
    *   *Ans*: Connect the workspace to Git. Commit the notebook to a 'Dev' branch. Use a Pull Request to merge into 'Main'. A release pipeline (or Fabric Deployment Pipeline) then syncs the 'Main' branch to the 'Prod' workspace.
2.  **"A Fabric capacity is consistently at 100% utilization. What steps do you take?"**
    *   *Ans*: Use the Capacity Metrics App to find the top consumers. Look for long-running Spark jobs or heavy Power BI refreshes. Implement "Smoothing" analysis to see if it's a temporary peak or sustained load.
3.  **"What are 'Deployment Rules' and why are they used?"**
    *   *Ans*: They allow you to change data source references or parameter values automatically when deploying from Dev to Prod, ensuring the Prod report doesn't point to the Dev database.

## Practice Task
*   Set up a mock Azure DevOps pipeline that triggers a validation check when a change is made to a `.py` file in a Git-integrated Fabric workspace.
