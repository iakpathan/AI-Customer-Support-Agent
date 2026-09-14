```markdown
# AI Customer Support Agent: Twitter Support Pipeline & Evaluation Harness

A production-grade, end-to-end data engineering and LLM pipeline built for a technical assessment, processing real-world customer support interactions from the Kaggle Twitter Customer Support dataset (focusing on American Airlines). 

This system integrates data ingestion, multi-turn thread graph reconstruction, PII redaction, a Retrieval-Augmented Generation (RAG) reply framework, and an automated LLM-as-judge evaluation harness.

---

## System Architecture Overview

1. **Ingestion & Thread Reconstruction:** Filters raw tweets targeting `@AmericanAir`, traversing response graph relationships recursively to stitch fragmented multi-turn chats into chronological conversations (deduplicated by root `tweet_id` to 10,428 unique threads).
2. **Intent Classification:** Evaluates customer messages across an 8-category inductively derived taxonomy using an LLM backend (`openai/gpt-oss-20b`).
3. **Retrieval-Augmented Generation (RAG):** Embeds historical resolutions using `sentence-transformers` (`all-MiniLM-L6-v2`) and matches them via `faiss-cpu` cosine similarity to ground generated draft replies in authentic brand voice and resolution workflows.
4. **Safety & Escalation Routing:** Combines the predicted intent with a deterministic keyword scan to drive automated handling decisions or trigger priority human handoffs.
5. **Evaluation Harness:** Employs an independent multi-dimensional LLM-as-judge scoring pipeline (assessing Groundedness, Relevance, Tone, and Actionability) validated against human ratings.

---

## Project Structure

```text
├── customer_support_pipeline.ipynb   # Main end-to-end execution notebook
├── hiver_support_agent_architecture.png # System architecture diagram asset
├── README.md                         # Project documentation and setup guide
└── report.pdf                        # Final 6-page technical documentation report

```

---

## Key Engineering Challenges & Solutions

The pipeline resolves several tangible real-world data and API friction points:

* **Thread Depth & Truncation:** Raised recursive thread-walking ceilings from 10 to 30 turns after empirical distribution analysis revealed threads spanning up to 21 turns.
* **API Availability & Model Shifts:** Migrated operational endpoints from deprecated Groq models (`llama-3.1-8b-instant`) to stable alternatives (`openai/gpt-oss-20b`).
* **Reasoning Token Management:** Configured explicit `max_tokens` boundaries and `reasoning_effort="low"` to prevent reasoning models from timing out or returning empty traces.
* **Quota Exhaustion & Persistence:** Implemented batch checkpointing using Python's `pickle` module alongside Google Drive syncing to survive runtime disconnections.
* **Keyword Ordering Bias:** Replaced fragile ordered `if/else` keyword checks with a multi-class scoring function that evaluates matches across all categories simultaneously.

---

## Setup and Reproduction

### Prerequisites

* Python 3.10+
* Google Colab environment (recommended) or local Jupyter setup.

### Required Dependencies

Install the core libraries directly in your environment:

```bash
pip install pandas numpy sentence-transformers faiss-cpu groq tqdm

```

### Running the Notebook

1. Open `customer_support_pipeline.ipynb` in Google Colab.
2. Ensure your API provider credentials (e.g., Groq API key) are securely loaded into your environment variables (`os.environ["GROQ_API_KEY"]`).
3. Execute the cells sequentially. The data ingestion and vector indexing blocks automatically handle checkpointing and caching via local/Drive persistence to resume safely if rate limits or interruptions occur.

---

## Author

**Pattan Munwar Ali Khan**

*Contact:* pathanali2005@gmail.com

```

```
