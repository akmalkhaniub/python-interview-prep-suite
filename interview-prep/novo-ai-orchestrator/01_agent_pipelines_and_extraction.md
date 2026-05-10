# Deep Dive: Agent Pipelines & Medical Extraction

Novo AI's core value is turning messy medical documents into structured data using self-improving agent loops.

## 1. Designing the "Self-Improver" Loop
*   **Step A: Extraction:** Agent 1 parses the hospital invoice and produces JSON.
*   **Step B: Critique:** Agent 2 (a "Critic" with access to medical coding standards) reviews the JSON for inconsistencies.
*   **Step C: Refinement:** If Agent 2 finds an error, Agent 1 is re-prompted with the critique to fix the data.
*   **Step D: Metadata Logging:** The system logs how many "Critique" rounds were needed, using this data to identify which document types need better base prompts.

## 2. Architecting the Extraction Pipeline
Instead of one massive prompt, Novo AI engineers build a fleet of specialized agents:
- **The Router:** Determines the document type (Invoice vs. Pre-auth).
- **The OCR Cleaner:** Fixes common OCR errors in names and numbers.
- **The Table Extractor:** Focuses specifically on the "Line Items" of a hospital bill.
- **The Validator:** Ensures the final JSON schema matches the insurance company's database requirements.

## 3. Spec-Driven System Design
*   **The Philosophy:** You don't write the Python code for the parser; you write the **Markdown Specification** that describes the desired behavior.
*   **The Orchestration:** You use **Claude Max** to generate the boilerplate, the business logic, and the unit tests. You then use **Cursor** to run and verify the generated code on a remote dev box.

## 4. Evaluation for High-Stakes Data
*   **Golden Datasets:** Every document type should have 100+ "perfect" examples.
*   **Semantic Drift:** Monitoring if the agents start hallucinating new medical codes that don't exist in the standard ICD-10 or CPT databases.

## Interview Questions
1.  "How would you design a multi-agent system to extract data from a hand-written medical record? What are your validation gates?"
2.  "Describe your process for building a 'Self-Correcting' loop. How do you prevent an agent from getting stuck in an infinite loop of critiques?"
3.  "If Claude generates code that has a subtle logic bug, how do you use the AI to identify and fix it without typing the fix yourself?"
4.  "What are the metrics for success in a medical document extraction pipeline? (Focus on precision, recall, and cost-per-document)."
