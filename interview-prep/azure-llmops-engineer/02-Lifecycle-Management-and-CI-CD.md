# 02: Lifecycle Management and CI/CD

LLMOps requires specialized CI/CD patterns, especially for managing prompts and non-deterministic models.

## 1. Prompt Lifecycle Management
*   **Prompts as Code**: Storing prompt templates in Git (YAML or JSON).
*   **Versioning**: Assigning versions to prompts so you can roll back if a new prompt degrades performance.
*   **Testing**: Running "Evaluation Suites" (e.g., using RAGAS or G-Eval) as part of the PR process.

## 2. CI/CD for LLM Workflows
*   **GitHub Actions / Azure DevOps**: Automating the deployment of:
    1.  Azure Functions (code).
    2.  Infrastructure as Code (Bicep/Terraform).
    3.  Model configurations (temperature, max tokens).
*   **Blue/Green Deployment**: Routing a small percentage of traffic to a new RAG configuration to test in production before a full rollout.

## 3. Azure Machine Learning (AzureML)
*   **Components & Pipelines**: Creating reusable steps for embedding generation, evaluation, and deployment.
*   **Model Registry**: Even if using Azure OpenAI (SaaS), you can track which specific model version (e.g., `gpt-4-0613`) is active in which environment.
*   **Prompt Flow**: Integrating Azure AI Foundry's Prompt Flow into the CI/CD pipeline for automated batch testing.

## Interview Questions
1.  **"How is CI/CD for an LLM application different from traditional software?"**
    *   *Ans*: The "code" includes prompts which are sensitive to model version changes. We need "Evaluation pipelines" that run during CI to check for regressions in answer quality, not just unit tests for code logic.
2.  **"Explain how you would version-control a prompt in a production system."**
    *   *Ans*: I'd store the prompt in a central repository (e.g., a `.yaml` file). When the code runs, it pulls the prompt version specified in the environment variables. Updating the prompt involves a PR, a quality check, and an update to the config.
3.  **"How do you handle schema drift in your vector database?"**
    *   *Ans*: I use Infrastructure as Code (Bicep/Terraform) to manage the Azure AI Search index schema. Any change to the index fields must go through a CI/CD pipeline that migrates the index or re-indexes data if necessary.

## Practice Task
*   Create a simple YAML schema for a prompt template that includes `template`, `input_variables`, and `model_params`.
