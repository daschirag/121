# Ingestion Checkpoint Demo

A minimal, standalone slice of the tech-debt quantification pipeline: **Layer 1
(ingestion)** only. No Docker, no Neo4j, no Qdrant, no GPU — just Python and a
few pip packages.

## Setup & run

### Windows (PowerShell)

```powershell
cd demo\ingestion-checkpoint
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python -m src.ingestion.ast_parser tests/fixtures/mini_repo
```

### macOS / Linux (bash)

```bash
cd demo/ingestion-checkpoint
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m src.ingestion.ast_parser tests/fixtures/mini_repo
```

Run all commands from the `demo/ingestion-checkpoint` folder so `src` resolves
as a package (`python -m` adds the current directory to `sys.path`). If you
run the script from elsewhere, set `PYTHONPATH` to this folder instead:

```bash
PYTHONPATH=. python -m src.ingestion.ast_parser tests/fixtures/mini_repo
```

## What this demonstrates

This is **Layer 1 of a 6-layer tech debt quantification pipeline**:
AST-based multi-language parsing (Python, JavaScript, TypeScript, Java, Go,
Rust) that walks a repository and extracts functions, classes, imports, and
call sites using `tree-sitter`, plus git history analysis (`git_crawler.py`)
that computes change-frequency, blame, and last-modified metrics for
identifying churn-prone, high-risk code. Together these outputs feed the
graph and scoring layers of the full pipeline.

Layers 2–6 (causal graph propagation, RAG-based retrieval, agentic refactor
generation, etc.) are implemented in the full project but require
Neo4j/Qdrant/GPU infrastructure that is intentionally out of scope for this
ingestion-layer checkpoint.
