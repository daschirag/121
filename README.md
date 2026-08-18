# Repo-Level Technical Debt Quantification Agent — Checkpoint Demo

**B.Tech IT Capstone Project · VIT Vellore · 2025-26**

This repository contains a working, standalone slice of a larger 6-layer system that
automatically detects, causally ranks, and proposes fixes for technical debt across an
entire codebase — going beyond flat static-analysis tools like SonarQube, which score
each file in isolation and have no way of knowing that a simple-looking function might
be *called by* dozens of complex, high-churn modules and is therefore a much bigger risk
than its own complexity number suggests.

## What this checkpoint demonstrates

This is **Layer 1 of 6** in the full pipeline: **Repository Ingestion.**

Given any Git repository, this layer:

1. **Parses every source file** using `tree-sitter` (an incremental AST parser) across
   six languages — Python, JavaScript, TypeScript, Java, Go, and Rust — extracting every
   function definition, class definition, import statement, and function call into a
   structured record (name, file, line range, enclosing scope).
2. **Crawls the full Git commit history in a single pass** to compute a change-frequency
   count per file — how often each file has been modified over the project's lifetime,
   a strong signal of which code is actively evolving (and therefore more likely to
   accumulate debt or introduce regressions).

This layer has already been validated against a real-world codebase (Flask, ~83 files,
~1,460 functions) with zero parse failures, running in under a second.

## What the full system does (beyond this checkpoint)

The complete pipeline — implemented but not included in this lightweight demo, since it
requires a graph database, a vector database, and a local LLM — takes this output and:

- **Layer 2:** builds a directed call graph in Neo4j, linking every function to the
  functions it calls, with careful disambiguation so that common function names (like
  `get`) don't get incorrectly linked across unrelated parts of the codebase.
- **Layer 3 (the core contribution):** propagates a "debt impact score" through that
  call graph — so a simple function that's called by many complex, frequently-changed
  functions is correctly flagged as high-risk, even though its own complexity is low.
  This causal propagation is the key idea that distinguishes this system from existing
  tools, which only look at each file on its own.
- **Layer 4:** embeds code documentation into a vector database (Qdrant) so the system
  can retrieve relevant context about *why* code was written a certain way.
- **Layer 5:** an AI agent that combines the debt ranking, the call-graph context, and
  the retrieved documentation to generate specific, grounded refactoring suggestions
  using a local large language model (StarCoder2-7B).
- **Layer 6:** a web API and dashboard exposing the ranked debt report, suitable for
  integration into a CI/CD pipeline as an automated code-quality gate.

## Setup & run (this checkpoint only)

Requires Python 3.11+. No Docker, no database, no GPU needed for this layer.

### Windows (PowerShell)
```powershell
git clone https://github.com/daschirag/121.git
cd 121
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python -m src.ingestion.ast_parser tests/fixtures/mini_repo
```

### macOS / Linux
```bash
git clone https://github.com/daschirag/121.git
cd 121
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m src.ingestion.ast_parser tests/fixtures/mini_repo
```

You should see output listing every function, class, import, and call site found in the
sample repository, followed by summary statistics (files parsed, total functions,
classes, imports, calls found).

To try it against a bigger, real-world repository instead of the small sample:
```bash
git clone https://github.com/pallets/flask.git some-folder-name
python -m src.ingestion.ast_parser some-folder-name
```

## Why this matters (talking points for presenting)

- Existing tools (SonarQube, GitHub Copilot) either score files in isolation or have no
  repository-wide awareness at all — this project's core novelty is *causal* debt
  scoring that accounts for how risk cascades through a codebase's actual call structure.
- This ingestion layer is the foundation everything else builds on — it has to correctly
  and efficiently extract structure from real code before any causal analysis is
  possible, which is why it's the first checkpoint.
- Validated at real-world scale (Flask framework: 83 files, 1,460 functions, 0 parse
  failures, sub-second runtime).
