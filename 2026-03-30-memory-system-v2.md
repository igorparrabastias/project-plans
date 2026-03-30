# Plan: 3-Tier Memory Architecture Enhancement

Based on the principles outlined in the referenced video (https://www.youtube.com/watch?v=LHoB1C2H3f0).

## Objective
Evolve the agent's memory system to a 3-tier architecture for improved recall, reduced token dependency, and autonomous operation.

## System Constraint Analysis
- **Host:** Google Cloud VM, type `e2-medium` (2 vCPUs, 4 GB RAM).
- **Primary Concern:** Tier 2 implementation (local semantic search via Ollama) may exceed the available RAM of this instance, leading to performance degradation or failure. This requires careful model selection and testing.

## Proposed Architecture

### Tier 1: Working Memory (Human/Agent Layer)
- **Technology:** Filesystem-based, compatible with Obsidian.
- **Implementation:**
    -   Define a directory structure for daily logs, active goals, and short-term scratchpads.
    -   Develop agent skills to read/write to this layer consistently.
    -   This will replace or augment the current `MEMORY.md` for volatile data.

### Tier 2: Semantic Memory (Long-Term Recall)
- **Technology:** Ollama + Vector Database.
- **Implementation:**
    -   **[INVESTIGATION REQUIRED]** Research and select a lightweight language model from Ollama compatible with the `e2-medium`'s resource constraints.
    -   **[INVESTIGATION REQUIRED]** Select a lightweight vector store/library (e.g., ChromaDB, FAISS).
    -   Develop a process to ingest data from Tier 1 and other sources (e.g., historical logs) into the vector store.
    -   Implement a hybrid search function (vector + keyword) for retrieval.

### Tier 3: Hard Data (Deterministic Storage)
- **Technology:** SQLite.
- **Implementation:**
    -   Define a schema for storing structured data (e.g., user preferences, project details, credentials).
    -   Create agent skills to perform CRUD operations on the SQLite database.
    -   This will be the source of truth for specific, non-negotiable facts.

## Core Processes

### "Dream Cycle" (Nightly Maintenance)
- **Technology:** Cron job executing a shell script.
- **Implementation:**
    -   Script to archive/summarize Tier 1 logs older than a defined threshold (e.g., 72 hours).
    -   Script to re-index Tier 2 data if necessary.
    -   Script to perform SQLite maintenance (`VACUUM`).
    -   Clear the active context window for a fresh start.

## Next Steps
1.  Confirm the proposed plan and architecture.
2.  Select a directory for the Tier 1 memory store.
3.  Begin investigation into a suitable lightweight model for Ollama.
4.  Define the initial schema for the Tier 3 SQLite database.
