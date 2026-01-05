Here is **one single, complete, clean Markdown README** with **everything consolidated**.
You can copy-paste this **as-is** into `README.md`.

---

# Explainable Semantic Search for Enterprise Knowledge Bases

## Overview
This project implements an **explainable, enterprise-grade semantic search system** for internal knowledge bases, modeled after how real **GenAI “buddy” systems** used by engineering and IT teams operate.

The system retrieves **section-level evidence**, deduplicates results at the **document level**, prioritizes **actionable remediation**, filters weak matches, and **explicitly signals uncertainty** when relevance is partial.

It is designed to be **trust-first**, conservative, and explainable  - not a hallucinating chatbot.

---

## Key Capabilities

- Semantic retrieval using sentence embeddings + FAISS
- Section-aware ranking (Resolution > Root Cause > Symptoms)
- Document-level deduplication with supporting evidence
- Resolution-first bias when relevance is comparable
- Weak-result filtering to reduce noise
- Explicit confidence signaling (LOW / MEDIUM / HIGH)
- Transparent explanations for ranking decisions
- No LLM-generated answers (retrieval-only, grounded)

---

## Architecture

```

data/
└── docs/
├── pega_troubleshooting/     # Synthetic Pega developer KBs
└── public_it_runbooks/       # Public IT runbooks (NIST-inspired)

vector_store/
├── faiss.index
└── metadata.pkl

chunker.py     # Markdown-aware chunking
ingest.py      # Document ingestion
embed.py       # Embedding + FAISS indexing
search.py      # Retrieval, ranking, deduplication, explainability
api.py         # Thin FastAPI wrapper

````

---

## Retrieval Pipeline (High-Level)

1. Encode the user query using sentence-transformer embeddings
2. Retrieve top-K candidate chunks via FAISS
3. Apply section-aware score boosts
4. Deduplicate chunks into document-level results
5. Prefer **Resolution Steps** when relevance scores are close
6. Filter out weak secondary matches
7. Assign confidence level and explanation
8. Return explainable, conservative results

---

## Section-Aware Ranking Logic

| Section Type | Behavior |
|------------|---------|
| Resolution Steps | Highest priority when relevance is close |
| Root Cause | Secondary boost |
| Symptoms | Diagnostic context |
| Others | Neutral |

This ensures **actionable guidance is surfaced first** without ignoring diagnostic context.

---

## Confidence Semantics

| Confidence | Meaning |
|----------|--------|
| LOW | No sufficiently relevant document found |
| MEDIUM | Partial intent alignment; may require user judgment |
| HIGH | Strong semantic and procedural alignment |

The system **never inflates confidence**.

---

## Example API Response

```json
{
  "summary": "A partially relevant knowledge base article was found, but it may not fully address the query intent.",
  "results": [
    {
      "score": 0.629,
      "title": "Adding a Report to the Insights Tab in a Constellation Application",
      "source": "pega",
      "path": "data/docs/pega_troubleshooting/adding_report_to_insights_tab_constellation.md",
      "primary_section": "Resolution Steps",
      "supporting_sections": ["Symptoms", "Root Cause", "Best Practices"],
      "reason": "Provides remediation guidance related to adding a report to the insights tab in a constellation application"
    }
  ],
  "confidence": "MEDIUM",
  "explanation": "Results are ranked based on semantic similarity, with preference given to actionable remediation when relevance is comparable. Weak matches are excluded to reduce noise."
}
````

---

## Explainability Strategy

Each response explains:

* **Why this document was selected**
* **Which section drove relevance**
* **Why weaker documents were excluded**

This reduces black-box behavior and increases user trust.

---

## Tech Stack

* Python
* Sentence-Transformers (`all-MiniLM-L6-v2`)
* FAISS
* FastAPI
* Markdown-based knowledge bases

---

## Design Principles

* Retrieval-first, not generation-first
* No hallucination
* Conservative confidence signaling
* Explainability over completeness
* Clear separation of concerns
* Enterprise-safe behavior

---

## Intended Use Cases

* Internal developer support assistants
* IT / platform knowledge base search
* GenAI “buddy” backends
* Explainable semantic retrieval systems

---

## What This Project Is *Not*

* ❌ A chatbot that invents answers
* ❌ A streaming ingestion pipeline
* ❌ An autonomous reasoning agent

This system prioritizes **correctness, transparency, and trust**.

---

## Running the Project Locally

```bash
pip install -r requirements.txt
python ingest.py
python embed.py
uvicorn api:app --reload
```

Swagger UI:

```
http://127.0.0.1:8000/docs
```

---

## Why This Project Matters

Most GenAI demos optimize for flash.
This project optimizes for **real-world enterprise constraints**:

* partial information
* ambiguity
* explainability
* trust

It reflects how **actual internal GenAI platforms** are built and evaluated.

---
