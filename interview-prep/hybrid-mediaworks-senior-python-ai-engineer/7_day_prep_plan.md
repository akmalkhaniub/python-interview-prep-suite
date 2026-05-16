# 7-Day Interview Preparation Plan: Senior Python AI/ML Engineer

This plan targets the specific blend of robust backend engineering (FastAPI/Django), cutting-edge AI (LangGraph/RAG), and data engineering (Playwright/Scrapy) required by Hybrid Mediaworks.

## Day 1: Advanced Python & Async Backend (FastAPI/Django)
- **Action:** Review FastAPI's async dependency injection and background tasks. Brush up on Django's ORM and Admin architecture.
- **Focus:** Understand the GIL (Global Interpreter Lock), `asyncio` mechanics, and how to prevent blocking the event loop when dealing with ML models. 
- **Output:** Be able to white-board an architecture showing FastAPI interacting with a Celery worker for an AI task.

## Day 2: RAG, Vector Databases & Embeddings
- **Action:** Deep dive into Pinecone and ChromaDB.
- **Focus:** Differentiate between HNSW algorithms, cosine similarity vs. dot product, and advanced RAG techniques (Parent-Child document retrieval, Query Re-writing, Hybrid Search).
- **Output:** Explain the trade-offs between dense (embedding) and sparse (BM25) vector retrieval.

## Day 3: LangChain & LangGraph (Agentic AI)
- **Action:** Solidify your understanding of stateful multi-agent systems.
- **Focus:** Review the syntax and theory behind LangGraph. How are graphs compiled? How is state managed between nodes? How do you implement a "Supervisor" agent?
- **Output:** Write out a pseudo-code workflow of a LangGraph agent designed to scrape a site, summarize the data, and email a report.

## Day 4: Web Scraping Pipeline Architecture (Playwright/Scrapy)
- **Action:** Prepare for data engineering questions.
- **Focus:** Scrapy spiders, Item Pipelines, middlewares for proxy rotation, and Playwright for JS rendering. 
- **Output:** Outline a scalable, distributed scraping architecture capable of extracting 100k pages a day without being blocked.

## Day 5: ML Models & Computer Vision (YOLO/OpenCV)
- **Action:** Review foundational ML and CV.
- **Focus:** Scikit-learn (Random Forest, XGBoost), PyTorch basics, and the YOLO architecture (object detection, bounding boxes, IoU, non-max suppression). OpenCV basics (image transformations, filtering).
- **Output:** Be prepared to discuss how you would fine-tune a YOLO model on a custom dataset and deploy it via FastAPI.

## Day 6: MLOps, Kubernetes & Observability
- **Action:** Review deployment and monitoring strategies.
- **Focus:** Dockerizing Python apps, Kubernetes concepts (Deployments, Services, Ingress), and LLM observability (LangSmith, prompt monitoring, token cost tracking).
- **Output:** Draw a deployment pipeline from `git push` to a live K8s cluster running an LLM API.

## Day 7: Mock Interview & Portfolio Review
- **Action:** Practice explaining your past projects using the STAR method.
- **Focus:** Highlight your end-to-end capabilities: "I scraped the data with Playwright, built the RAG pipeline with Pinecone and LangGraph, and served it via a highly concurrent FastAPI backend." 
- **Output:** Prepare to discuss working the 04:00 PM to 01:00 AM on-site shift in Islamabad, showing enthusiasm for the team collaboration.
