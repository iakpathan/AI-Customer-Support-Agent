# AI Customer Support Agent

An AI support agent for American Airlines (Kaggle Twitter Customer Support dataset)
that classifies incoming messages into 8 intents, drafts replies grounded in
historically similar resolutions (RAG), and decides auto-handle vs. escalate
with a stated reason.

##Repository Structure
AI-Customer-Support-Agent/
├── README.md
├── requirements.txt
├── .gitignore
├── data/
│   ├── golden_set_blind.csv          - 187 conversations, blind human-labeled ground truth
│   ├── classification_results.csv    - 762 LLM-classified threads
│   ├── judge_scores.csv              - LLM-as-judge scores for 40 drafted replies
│   ├── human_judge_validation.csv    - 15 blind human ratings, validates the judge
│   └── retrieval_demo_corpus.pkl     - 1,000-thread sample for the live pipeline demo
├── notebooks/
│   ├── 01_development_and_exploration.ipynb  - full development process: data pipeline,
│   │                                            debugging, prompt iteration (see report
│   │                                            "Engineering Challenges" for highlights)
│   ├── 02_reproduce_results.ipynb            - regenerates every report table from
│   │                                            data/*.csv (no API key needed, ~1 minute)
│   └── 03_test_pipeline.ipynb                - runs the live agent end-to-end on any
│                                                message (classify, retrieve, draft,
│                                                escalate; requires a Groq API key)


## Setup

```bash
git clone https://github.com/iakpathan/AI-Customer-Support-Agent.git
cd AI-Customer-Support-Agent
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate     # macOS/Linux
pip install -r requirements.txt
```

## Reproducing the Reported Results (recommended first step)

Open `notebooks/02_reproduce_results.ipynb` in Jupyter or VS Code and run all cells
top to bottom (Kernel → Restart & Run All). It loads the committed CSVs in `data/`
directly — **no API key required**, runs in under a minute — and regenerates:

- The three-way baseline comparison (Trivial 18.7% · Keyword 49.7% · LLM 69.0%)
- The per-category accuracy breakdown and confusion matrix
- The LLM-as-judge vs. human validation table (93% overall agreement)

All numbers should match `report/hiver_report.pdf` exactly.

## Testing the Live Pipeline

Open `notebooks/03_test_pipeline.ipynb`. This runs the actual agent on a real
message you provide: intent classification → grounded retrieval → reply
drafting → escalation decision.

**Before running:**
1. Get a free Groq API key at [console.groq.com](https://console.groq.com)
2. Run the notebook top to bottom — it will prompt you to paste your API key
   securely (input is hidden, not stored in the notebook)
3. Edit the `test_message` variable in the demo cell to try your own examples

This notebook uses a 1,000-thread sample (`data/retrieval_demo_corpus.pkl`) for
retrieval rather than the full 10,428-thread corpus, to keep the repo lightweight.
See `01_development_and_exploration.ipynb` for the full-scale pipeline.

## Development Process

`notebooks/01_development_and_exploration.ipynb` contains the complete,
unedited development history — data pipeline construction, every debugging
step, and the V1/V2 prompt-iteration experiment. This is the working log
referenced throughout the report's "Engineering Challenges" section; it is
not the reproduction entry point (use `02_reproduce_results.ipynb` for that).

## Key Results Summary

| Method | Intent Classification Accuracy |
|---|---|
| Trivial (majority class) | 18.7% |
| Simple (keyword classifier) | 49.7% |
| **LLM classifier (openai/gpt-oss-20b)** | **69.0%** |

LLM-as-judge validated against 15 blind human ratings: **93% overall agreement**
(within ±1 point across Groundedness, Relevance, Tone, Actionability).

