# Deep Dive: Automotive Data Pipelines & Cleaning

In the automotive industry, data is notoriously fragmented across thousands of dealers and manufacturers.

## 1. The Challenges of Automotive Data
*   **VIN Inconsistency:** Handling partial VINs (first 10-11 digits) for decoding Year/Make/Model without the serial number.
*   **Taxonomy Chaos:** Mapping "F-150 Lariat" vs "Ford F150 Lariat" vs "F150 Lariat Supercrew" to a single canonical entity.
*   **Stale Listings:** Detecting when a car is sold but remains listed in the ingestion source.

## 2. Entity Resolution & Deduplication
*   **The Problem:** The same vehicle listed on Dealer Site A, Craigslist, and an Auction site.
*   **The Strategy:**
    1.  **Block:** Group by Year and ZIP code to reduce comparison space.
    2.  **Score:** Compare VIN (if available), Price, Odometer, and Color.
    3.  **Merge:** Use a "Source of Truth" hierarchy (e.g., Manufacturer data > Dealer data > Scraped data).

## 3. High-Volume Ingestion (Python)
*   **Async Scrapers:** Using `Playwright` or `Scrapy` with async patterns to crawl thousands of dealer pages without blocking.
*   **Data Validation (Pydantic):**
    ```python
    from pydantic import BaseModel, validator

    class VehicleListing(BaseModel):
        vin: str
        price: float
        odometer: int

        @validator('vin')
        def validate_vin(cls, v):
            if len(v) != 17:
                raise ValueError('Incomplete VIN')
            return v.upper()
    ```

## 4. Normalization Pipelines
*   **Mapping Tables:** Using PostgreSQL lookup tables to normalize "Met. Blue" -> "Blue".
*   **Enrichment:** Taking a VIN and calling an external API (like NHTSA) to pull engine specs and safety ratings.

## Interview Questions
1.  "How do you ensure data quality when you are ingesting data from 50+ different dealer management systems (DMS)?"
2.  "Describe a time you handled a massive deduplication task. What algorithms or tools did you use?"
3.  "How do you handle 'Schema Drift' when an external source changes their data format unexpectedly?"
4.  "What are the pros and cons of 'Normalization' at the ingestion layer vs. the query layer?"
