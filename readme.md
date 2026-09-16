# AI Customer Support Agent 

An AI support agent for American Airlines (Kaggle Twitter Customer Support dataset)
that classifies incoming messages into 8 intents, drafts replies grounded in
historically similar resolutions (RAG), and decides auto-handle vs. escalate
with a stated reason.

## Repository Contents

| File | Description |
|---|---|
| `development_exploration.ipynb` | Full development process: data pipeline construction, every debugging step, and the V1/V2 prompt-iteration experiment. This is the working log referenced throughout the report's "Engineering Challenges" section. Not the reproduction entry point. |
| `reproducing_results.ipynb` | Regenerates every table and number in the report directly from the committed CSVs below. No API key needed, runs in under a minute. |
| `test_pipeline.ipynb` | Runs the live agent end-to-end on any message you provide: intent classification, grounded retrieval, reply drafting, and escalation decision. Requires a free Groq API key. |
| `golden_set_blind.csv` | 187 conversations, blind human-labeled ground truth |
| `classification_results.csv` | 762 LLM-classified threads (predicted intent) |
| `judge_scores.csv` | LLM-as-judge scores for 40 drafted replies |
| `human_judge_validation.csv` | 15 blind human ratings, used to validate the judge |
| `retrieval_demo_corpus.pkl` | 1,000-thread sample used for retrieval in `test_pipeline.ipynb` |
| `.gitignore` | Excludes `venv/`, `.env`, caches, etc. |

## Setup

```
git clone https://github.com/iakpathan/AI-Customer-Support-Agent.git
cd AI-Customer-Support-Agent
python -m venv venv
venv\Scripts\activate
```
(macOS/Linux: `source venv/bin/activate` instead of the line above)

```
pip install pandas numpy groq sentence-transformers faiss-cpu python-dotenv tqdm
```

## Reproducing the Reported Results (recommended first step)

Open `reproducing_results.ipynb` in Jupyter or VS Code and run all cells top to
bottom (Kernel -> Restart & Run All). It loads the CSVs in this repo directly --
no API key required, runs in under a minute -- and regenerates:

- The three-way baseline comparison (Trivial 18.7% / Keyword 49.7% / LLM 69.0%)
- The per-category accuracy breakdown and confusion matrix
- The LLM-as-judge vs. human validation table (93% overall agreement)

## Testing the Live Pipeline

Open `test_pipeline.ipynb`. This runs the actual agent on a real message you
provide: intent classification -> grounded retrieval -> reply drafting ->
escalation decision.

Before running:
1. Get a free Groq API key at console.groq.com
2. Run the notebook top to bottom -- it will prompt you to paste your API key
   securely (input is hidden, not stored in the notebook)
3. Edit the `test_message` variable in the demo cell to try your own examples

This notebook uses a 1,000-thread sample (`retrieval_demo_corpus.pkl`) for
retrieval rather than the full 10,428-thread corpus, to keep the repo
lightweight. See `development_exploration.ipynb` for the full-scale pipeline.

## Key Results Summary

| Method | Intent Classification Accuracy |
|---|---|
| Trivial (majority class) | 18.7% |
| Simple (keyword classifier) | 49.7% |
| LLM classifier (openai/gpt-oss-20b) | 69.0% |

LLM-as-judge validated against 15 blind human ratings: 93% overall agreement
(within +/-1 point across Groundedness, Relevance, Tone, Actionability).


