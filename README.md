# Resilient Memory for LLMs

[![Python 3.11+](https://img.shields.io/badge/Python-3.11%2B-blue?logo=python)](https://www.python.org/)
[![Rust WIP](https://img.shields.io/badge/Rust-Commercial%20Edition%20WIP-orange)](https://www.rust-lang.org/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.x-EE4C2C?logo=pytorch&logoColor=white)](https://pytorch.org/)
[![NumPy](https://img.shields.io/badge/NumPy-2.x-013243?logo=numpy&logoColor=white)](https://numpy.org/)
[![Pandas](https://img.shields.io/badge/pandas-2.x-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Pydantic](https://img.shields.io/badge/pydantic-2.x-0A7)](https://docs.pydantic.dev/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-3.x-11557c)](https://matplotlib.org/)
[![Qdrant](https://img.shields.io/badge/Qdrant-offline%20vector%20DB-FF6B6B)](https://qdrant.tech/)
[![Ollama](https://img.shields.io/badge/Ollama-local%20LLM-000000)](https://ollama.com/)
[![Air–gapped](https://img.shields.io/badge/Air--gapped-supported-brightgreen)](#offline-and-air-gapped)
[![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue)](LICENSE)

A next-generation memory layer for large language models designed to stay stable, useful, and fast over long horizons. It is inspired by biological systems and guided by formal mathematics, without relying on ever-growing context windows.

## TLDR;

A controller prototype around an LLM that maintains bounded internal “hormone” state (e.g., stress, curiosity), composes those into safe control knobs (e.g., temperature, top_p), and applies layered coping (timeouts, resets, baselines) to keep behavior stable. This repo provides a working Python prototype, small simulations, and math notes.

What’s included now
- `src/gnn_controller.py`, `src/resilient_controller.py`, `src/content_processor.py`
- A minimal, reproducible simulation in `src/resilient_controller.py::test_resilient_chaos()`

What’s not included yet
- Production adapters, a full memory pipeline, and external benchmarks

How it works (tight loop)
1.	Signals (novelty, errors, stress) trigger hormone impulses → decay keeps them bounded.
2.	Hormones compose control knobs via a weighted, clamped blend (guaranteed in-range).
3.	The system retrieves, tools, and prompts under those knobs.
4.	Outcomes are scored (task/quality/safety/efficiency + memory score).
5.	Value/advantage is computed; policy params that map hormones→knobs are updated.
6.	Resilience may reset stress or pause inputs to guarantee recovery.
7.	If memory is optimal, the item is consolidated; credit is tracked for future retrieval.

Why it’s different (prototype scope)
- Bounded internal state with explicit decay and clamps
- Layered coping that reduces the chance of prolonged instability
- Knob mapping separated from the base model (provider-agnostic)
- Inspectable behavior via a small hormone vector and modes

Evidence and limitations
- The toy simulation shows higher "in-range" control rate with resilience layers enabled. Reproduce via `src/resilient_controller.py::test_resilient_chaos()`.
- Results are preliminary and scenario-specific. They are not a statement about real-world reliability; external replication and broader tasks are future work.

## Why this is different
- **Biologically inspired dynamics**: adaptive write/forget signals and hormone-like decay
- **Graph-aware consolidation**: memories interact over a sparse graph scored by a value function
- **Resilience-first design**: safeguards against runaway growth, catastrophic forgetting, and noise
- **Theory-backed**: convergence and boundedness under realistic assumptions
- **Practical wins**: early internal runs show strong recall and throughput gains vs naive retrieval

---

## Documentation

### Core Documentation
- **[BREAKTHROUGH.md](docs/BREAKTHROUGH.md)** - Technical breakthrough details and architecture overview
- **[FOUNDATION.md](docs/FOUNDATION.md)** - Mathematical foundations and formal proofs
- **[IMPACT.md](docs/IMPACT.md)** - White paper on research significance and applications
- **[whitepaper.md](docs/whitepaper.md)** - Comprehensive white paper with technical details
- **[wiki_page.md](docs/wiki_page.md)** - Complete project wiki and reference guide

### Project Documentation
- **[CODE_OF_CONDUCT.md](docs/CODE_OF_CONDUCT.md)** - Community guidelines and standards
- **[CONTRIBUTING.md](docs/CONTRIBUTING.md)** - How to contribute to the project
- **[SECURITY.md](docs/SECURITY.md)** - Security policy and vulnerability reporting

---

## Under the hood (what we can share now)
- **Consolidation engine**: Optional vector store with an in-memory fallback. Each item is embedded by a compact state vector `[stress, value, recency]`, clustered during "sleep cycles" and organized into quadrants: `crisis_learning`, `skill_building`, `exploration`, `routine_forget`.
- **Resilient controller**: Six human-like layers—Immediate Coping, Medication, Baseline Adaptation, Rest & Recovery, Time Perspective, Mode-Aware Learning—stabilize learning signals and prevent stress spirals.
- **Graph value function**: A lightweight GCN (`ValueGCN`) reads a fully connected hormone graph (including a global node) to estimate value and drive policy updates.
- **Knob policy**: A bounded control signal `k` combines baseline + per-hormone deltas with stress-adaptive weighting; learning is advantage-driven and mode-aware.

---

### Offline and air-gapped
- Runs fully local with optional self-hosted backends: Qdrant (vector DB) and Ollama (local LLM).  
- No external calls required for benchmarking and demos; suitable for air-gapped deployments.

---

## What you'll see here pre-release
- Concept notes and diagrams that outline the direction—without full blueprints  
- Selected benchmarking traces and visuals (see `results/`)  
- Minimal examples that hint at the interface and behavior  

### Bench snippets to watch
- Controller chaos tests moving from ~5.6% success (no resilience) to ~61% with resilience; advanced targets 75–80%.
- Memory stats that show short-term buffers consolidating into mid-term clusters and quadrant distributions over time.

## What stays private until launch
- Full architecture and training recipes  
- Memory Consoldation process source
- Full Docker Containers
- API interface layer and LLM model proxy  

## Our staff will be showing our technology at the NolaAi event from our founder.
Dustin T Hughes 
New Orleans Jazz Museum
400 Esplanade Ave, New Orleans, LA 70116
November 11, 2025 at 11:30 am.  

## Follow along
Watch this repository to get updates as we share more.  
