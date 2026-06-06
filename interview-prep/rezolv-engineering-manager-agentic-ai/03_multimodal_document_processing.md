# Module 03: Multimodal Document Processing

In lending workflows, underwriting speed is bounded by document processing. Rezolv uses multimodal vision and language pipelines to ingest, verify, and compare financial documents, accelerating loan origination and compliance.

---

## 1. Hybrid OCR-LLM Architecture
Extracting data from low-quality scanned bank statements, Aadhaar cards, PAN cards, or land deeds requires a hybrid approach to ensure accuracy and cost efficiency:

```
                            +--------------------------+
                            |     Input Document       |
                            | (PDF / JPEG / TIFF Scan) |
                            +------------+-------------+
                                         |
                                         v
                            +--------------------------+
                            |    Document Preprocess   |
                            | (Deskew, Binarization)   |
                            +------------+-------------+
                                         |
                       +-----------------+-----------------+
                       |                                   |
                       v                                   v
          +--------------------------+        +--------------------------+
          |     Vision LLM Path      |        |     OCR Engine Path      |
          |  (Claude-3.5-Sonnet /    |        |    (Tesseract / Google   |
          |   Gemini-1.5-Flash)      |        |        Document AI)      |
          +------------+-------------+        +------------+-------------+
                       |                                   |
           Structural  |                                   | Raw Text with
           Context     |                                   | Coordinates
                       +-----------------+-----------------+
                                         |
                                         v
                            +--------------------------+
                            |    Parser & Validator    |
                            |  (Structured JSON via    |
                            |     Pydantic / LLM)      |
                            +------------+-------------+
                                         |
                                         v
                            +--------------------------+
                            |      Output Database      |
                            +--------------------------+
```

---

## 2. Typical Lending Document Processing Tasks
1.  **Identity Documents (KYC):** Extracting name, date of birth, document numbers, and addresses from Aadhaar, PAN, and Voter ID cards, then verifying against government databases (e.g., NSDL, UIDAI via API).
2.  **Income Verification (Bank Statements):** Parsing tabular transactions from multi-page PDFs to compute average balance, salary deposits, bounce charges, and loan repayments.
3.  **Document Tampering / Fraud Detection:** Identifying Photoshop traces, metadata discrepancies, font mismatches, and structural layout anomalies in uploaded documents.

---

## 3. Comparing Vision LLMs vs. OCR engines
*   **Vision LLMs (e.g., Gemini-1.5-Flash, Claude 3.5 Sonnet):** Incredible at understanding complex structures (like hierarchy, keys and values, handwritten annotations). High cost, prone to minor numeric hallucinations if text is too small.
*   **OCR Engines (e.g., Google Document AI, Azure Read API):** Excellent raw text extraction accuracy (especially numbers/symbols). Bad at understanding context or structure out-of-the-box.
*   **Best Practice:** Use OCR to extract text coordinates and content, and feed the structured text schema alongside the image to a Vision LLM for parsing and validation.

---

## 4. Target Interview Questions & High-Score Answers

### Q1: How do you parse a 50-page scanned bank statement PDF to extract transaction rows in JSON format, without running out of LLM context tokens or incurring high costs?
**Answer:**
Parsing large bank statements requires chunking and hybrid extraction:
1.  **Chunking:** Split the PDF into individual pages and convert them to high-resolution PNGs (e.g., 200 DPI).
2.  **Raw Extraction:** Run a fast OCR engine (like Azure Read API or Google Document AI) on each page. This yields OCR text and bounding box positions.
3.  **Local Heuristic Filter:** Use rule-based python code (regex/heuristics) to isolate the transaction table boundaries.
4.  **Targeted LLM Extraction:** Pass the cropped table image or the structured OCR text of the table section to a fast model like `Gemini-1.5-Flash` (which is cheap and has a large context window) with a Pydantic schema enforcing transaction headers (`date`, `description`, `amount`, `type`, `balance`).
5.  **Reconciliation:** Sum the deposits and withdrawals on our side and verify they match the starting and ending page balances. If there is a mismatch, flag the file for manual human review.

### Q2: How do you detect document tampering (e.g., salary slip editing) using multimodal models?
**Answer:**
We construct a multi-layered detection pipeline:
1.  **Metadata Inspection:** Check PDF metadata for creation tools (e.g., Adobe Photoshop, Canva) and history of edits.
2.  **Acoustic/Visual Alignment:** Inspect text positioning. Edited text often has slight alignment or font differences. We train a lightweight CV model (YOLO or ResNet) to segment text lines and flag layout anomalies.
3.  **Semantic Validation:** We pass the document to a Vision LLM to verify that calculated metrics make mathematical sense (e.g., checking if the tax deductions match the Indian income tax slab for the stated gross salary). Discrepancies flag the document as suspected fraud.

### Q3: How do you handle document matching where the name on the PAN card ("R. K. Sharma") differs from the bank account name ("Raj Kumar Sharma")?
**Answer:**
We resolve this using a combination of deterministic rules and LLM-assisted fuzzy matching:
1.  **Normalization:** Strip special characters, honorifics (Mr., Dr.), and convert names to lowercase.
2.  **Fuzzy String Matching:** Compute Jaro-Winkler and Levenshtein distance scores. If the score is > 0.85, they are automatically linked.
3.  **LLM Entity Resolution:** If the score is in the ambiguous zone (e.g., 0.5 to 0.85), we send the names to a fast LLM with a prompt asking: "Are these two names likely representing the same person in an Indian banking context? Provide a confidence score and reasoning."
4.  **Verification Fallback:** Match external identifiers, such as PAN number printed on the bank statement, or matching addresses/birth dates across documents.

### Q4: Vision LLMs can hallucinate numeric values in tables. How do you design guardrails to ensure 100% financial accuracy in underwriting pipelines?
**Answer:**
We implement strict validation guardrails:
1.  **Dual Extraction:** Extract values using both a high-precision OCR engine and a Vision LLM. Cross-check the outputs.
2.  **Mathematical Invariant Validation:** In transaction tables, we assert that `Previous Balance + Deposit - Withdrawal = Current Balance` for every single row. If any row fails this constraint, the extraction is rejected.
3.  **Anchor Checks:** Check that the extracted totals match the summary metrics on the document (e.g., total debits/credits printed at the top/bottom).
4.  **Confidence Thresholding:** If the OCR confidence for any digit falls below 95%, or if the LLM output doesn't match OCR exactly, we route the page to our human-in-the-loop (HITL) dashboard for manual confirmation.
