# Module 01: Self-Healing Parsers & Selectors

This module covers the algorithms and system design required to build web scrapers that dynamically repair their extraction selectors and validate outputs programmatically.

---

## Technical Q&A

### Q1: Design a self-healing scraping script workflow. If a website changes its HTML structure (causing standard CSS selectors to return `None`), how does the scraper detect the failure and programmatically repair the selectors without human intervention?
**Answer:**
A **Self-Healing Scraper** uses a double-loop design. If the fast first-stage parser fails, the script triggers a slow second-stage AI repair cycle to generate new selectors, updating its registry to keep the system running.

#### 1. Self-Healing State Machine:
```
[Scraper Job Starts]
       │
       ▼
[Execute Fast Parse (lxml / CSS Selector)]
       │
       ├─► [Success: Output passes Validation Rules] ──► [Write to DB]
       │
       └─► [Failure: Returns None / Validation Fails]
             │
             ▼
      [Capture Full DOM / Screenshot]
             │
             ▼
      [Trigger AI Selector Repair (LLM / Semantic Search)]
             │
             ▼
      [Update Local Selector Registry] ──► [Retry Fast Parse]
```

#### 2. Technical Evasion Implementation:
*   **Failure Detection:** Do not just check if a selector returns `None`. Use schema rules. If a product page extracts the title and price, but the price is missing or the value fails range validation (e.g., extracting text instead of a number), flag the request as a selector failure.
*   **Selector Repair Algorithm:**
    1.  **DOM Serialization:** Extract a simplified snippet of the HTML surrounding the last known successful selector location (including element tag, text contents, attributes, and sibling structure).
    2.  **LLM Prompting:** Send the simplified DOM segment and the target field name (e.g., "Price") to a fast LLM (like GPT-3.5 or Gemini Flash):
        > "Given this HTML snippet, identify the element containing the price and return the most specific, robust CSS selector for it. Output strictly valid CSS selectors without markdown wrapping."
    3.  **Validation and Write-back:** The LLM returns a proposed selector (e.g., `.product-detail-price span`). Test the proposed selector against the current DOM. If it yields a valid, schema-compliant output, write the new selector back to the database registry to use for all future runs, and proceed with the data pipeline.

---

### Q2: How can vector embeddings be used to perform semantic DOM parsing? Explain how you would match an "Add to Cart" button across different sites whose class names are randomized hashes.
**Answer:**
When class names and HTML tag names are randomized or dynamic (such as compiled React/Tailwind elements), traditional selectors fail. Instead of querying names, we can represent HTML elements as **semantic vectors** and run similarity searches.

#### Implementation Pattern:
1.  **Element Vector Representation:**
    - For each interactive element (buttons, links, inputs) in the page DOM, extract key text features:
      - Inner text (e.g., "Add to Bag", "Purchase Now", "Checkout").
      - Element attributes (e.g., `id`, `name`, `type`, `role`, `aria-label`).
      - Parent element contexts (e.g., is it inside a `.product-purchase-container`?).
2.  **Embedding Generation:**
    - Concatenate these features into a structured text template:
      ```text
      element: BUTTON | text: Add to Bag | role: button | parent: div.product-box
      ```
    - Pass this text string to a local embedding model (like `nomic-embed-text` or `bge-small`) to generate a 384-dimensional dense vector representing the element's semantic meaning.
3.  **Vector Search Matching:**
    - Generate a target embedding vector for your reference action (e.g., embedding the text "Add to cart button").
    - Execute a cosine similarity query comparing the target vector against the vector embeddings of all buttons on the target page:
      $$\text{Similarity} = \frac{A \cdot B}{\|A\| \|B\|}$$
    - The button returning the highest similarity score (usually $> 0.85$) represents the target button, bypassing randomized class names entirely.

---

### Q3: Write a Python decorator using Pydantic to validate the outputs of a scraping function. If validation fails, capture the page source and log a detailed anomaly report.
**Answer:**
Below is the implementation using Pydantic to validate scraped dictionaries:

```python
import functools
import logging
from typing import Dict, Any, Optional
from pydantic import BaseModel, Field, ValidationError

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger("scraper_validator")

# Define target data schema
class ScrapedProductSchema(BaseModel):
    title: str = Field(..., min_length=3, description="Product title must be valid text.")
    price: float = Field(..., gt=0.0, description="Price must be a positive float.")
    in_stock: bool
    url: str = Field(..., min_length=10, description="Source URL must be valid.")

def validate_extraction(schema_class: typeof(BaseModel)):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            # Execute scraper function
            result: Dict[str, Any] = func(*args, **kwargs)
            
            # Assume page source HTML is passed or available in the context for logging
            html_source: Optional[str] = kwargs.get("html_source") or (args[1] if len(args) > 1 else None)
            
            try:
                # Validate raw dictionary against Pydantic schema
                validated_data = schema_class(**result)
                logger.info(f"Successfully validated data for: {validated_data.title}")
                return validated_data.model_dump()
                
            except ValidationError as e:
                logger.error(f"Validation failed for scraper function '{func.__name__}': {str(e)}")
                
                # Dump page source to a log file for debugging the layout change
                if html_source:
                    with open("failed_layout_dump.html", "w", encoding="utf-8") as f:
                        f.write(html_source)
                    logger.error("Saved failed page source to 'failed_layout_dump.html' for inspection.")
                
                # Return empty dictionary or raise custom error to trigger the self-healing process
                raise ValueError("Extraction output violates target Pydantic schema.")
        return wrapper
    return decorator

# Usage Example:
@validate_extraction(ScrapedProductSchema)
def scrape_ecom_page(url: str, html_source: str) -> Dict[str, Any]:
    # Mock parser output containing validation error (price is negative)
    return {
        "title": "Bluetooth Headset",
        "price": -19.99, 
        "in_stock": True,
        "url": url
    }
```
