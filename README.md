import os

# Define the content for the Markdown file
md_content = """# Project Guide: Explainable AML Graph-Reasoning Bot
**Domain:** Financial Technology / Compliance  
**Target:** JPMC Internal Hackathon  
**Core Tech:** Isolation Forest + Graph Neural Networks (GNN) + Large Language Models (LLM)

---

## 1. Project Purpose
The goal is to solve the "Black Box" problem in AML. We are moving from a system that simply flags a transaction to a system that **explains why** it was flagged, mapping out criminal networks and drafting a formal Suspicious Activity Report (SAR) automatically.

## 2. Where to Get the Data
Since you are using a personal laptop, you need high-quality synthetic data that mimics real bank transactions.

* **Source:** [IBM Transactions for Anti-Money Laundering (AML)](https://www.kaggle.com/datasets/ealtman2019/ibm-transactions-for-anti-money-laundering-aml)
* **Specific File to use:** Download `HI-Small_Trans.csv`. 
    * *Why?* The "Small" version is ~50MB, making it perfect for a laptop-based demo, whereas the "Large" version is several gigabytes.
* **Data Format:** This CSV contains: `Timestamp`, `From Bank`, `Account`, `To Bank`, `Account`, `Amount Received`, `Receiving Currency`, and `Payment Format`.

## 3. Where to Get the Models
You do not need to train these from scratch.

* **Isolation Forest:** Use the standard `scikit-learn` library. No pre-trained file needed as it fits in seconds.
* **GNN:** * Search Kaggle for "AML GNN" and look for notebooks with "Output" files.
    * **Recommended Weights:** Look for a `.pt` or `.pth` file trained on the IBM dataset.
    * **Alternative:** Use `Ymak7/HAMI-AML-DETECTOR` from Hugging Face if you want a pre-built model.
* **LLM:** * **Option A (Local):** Use **Ollama** to run `Llama-3-8B`. This works offline.
    * **Option B (API):** Use an OpenAI or Groq API key for faster, higher-quality reasoning.

## 4. Step-by-Step Implementation

### Step 1: The "Metal Detector" (Isolation Forest)
* **Input:** Raw CSV rows (Amount, Time, Currency).
* **Process:** Run `IsolationForest(contamination=0.01)`.
* **Result:** It flags the 1% most "mathematically weird" transactions. These become your **High-Interest Seeds**.

### Step 2: The "Deep Detective" (GNN)
* **Input:** The "High-Interest Seeds" + their neighbors (who they sent money to).
* **Process:** Convert these rows into a Graph structure (Nodes = Accounts, Edges = Transfers). Run your GNN inference.
* **Result:** The GNN assigns a "Network Risk Score." It identifies if the "weird" transaction is part of a "Fan-in" (many-to-one) or "Fan-out" (one-to-many) pattern.

### Step 3: The "Reporter" (LLM)
* **Input:** A text summary of the GNN results. 
    * *Example:* "Account A (Flagged by IF) sent $5k to 10 accounts. GNN found those 10 accounts are all connected to one offshore account."
* **Process:** Feed this into the LLM with a "System Prompt" (e.g., "You are a JPMC Compliance Officer").
* **Result:** A 3-paragraph SAR report.

## 5. Architecture Summary
1.  **Ingestion:** `pandas` reads the IBM CSV.
2.  **Triage:** `scikit-learn` (Isolation Forest) identifies the outliers.
3.  **Context:** `PyTorch Geometric` or `NetworkX` builds the local graph around outliers.
4.  **Reasoning:** `LangChain` sends the graph summary to `Llama-3`.
5.  **UI:** `Streamlit` shows the Dashboard (Graph on left, SAR Report on right).

## 6. How to Win (The Pitch)
When presenting to JPMC judges, emphasize these three points:
1.  **Scalability:** Isolation Forest handles millions of rows; GNN only looks at the "scary" ones.
2.  **Explainability:** We don't just provide a score; we provide a written legal justification.
3.  **Regulatory Compliance:** This system helps the bank avoid heavy fines by catching "Structuring" and "Layering" that traditional systems miss.
"""

# Save to a file
with open("/mnt/data/aml_explainable_ai_project_guide.md", "w") as f:
    f.write(md_content)
