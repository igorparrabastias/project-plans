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
    -   **[COMPLETED]** Defined a directory structure for daily logs, active goals, and short-term scratchpads.
    -   Location: `~/.openclaw/workspace/memory/`
    -   Structure:
        ```
        memory/
        ├── tier1_working/
        │   ├── daily/
        │   ├── goals/
        │   └── scratchpad.md
        ├── tier2_archive/
        │   ├── people/
        │   ├── projects/
        │   ├── concepts/
        │   └── procedures/
        └── tier3_structured/
        ```
    -   Develop agent skills to read/write to this layer consistently.
    -   This will replace or augment the current `MEMORY.md` for volatile data.

### Tier 2: Semantic Memory (Long-Term Recall)
- **Technology:** OpenClaw QMD Sidecar.
- **Implementation:**
    -   The QMD sidecar will be used as the semantic search engine, leveraging its built-in BM25, vector search, and reranking capabilities. This replaces the initial plan for a custom Ollama/vector store solution.
    -   OpenClaw will be configured to index `tier1_working` and `tier2_archive`.
    -   A process will be developed to ingest and consolidate data from Tier 1 into the structured `tier2_archive`.

### Tier 3: Hard Data (Deterministic Storage)
- **Technology:** SQLite.
- **Implementation:**
    -   A SQLite database will be located at `~/.openclaw/workspace/memory/tier3_structured/facts.sqlite`.
    -   Define a schema for storing structured data (e.g., user preferences, project details, credentials).
    -   Create agent skills to perform CRUD operations on the SQLite database.
    -   This will be the source of truth for specific, non-negotiable facts.

## Core Processes

### "Dream Cycle" (Periodic Maintenance)
- **Technology:** Cron job executing agent skills/scripts.
- **Implementation:**
    -   Skill to archive/summarize Tier 1 logs older than a defined threshold (e.g., 72 hours) and move them to Tier 2.
    -   QMD will handle its own re-indexing automatically.
    -   Skill/script to perform SQLite maintenance (`VACUUM`).
    -   Clear the active context window for a fresh start.

## Next Steps
1.  **[COMPLETED]** Confirm the proposed plan and architecture.
2.  **[COMPLETED]** Select a directory for the Tier 1 memory store.
3.  **[IN PROGRESS]** Implement the directory structure and OpenClaw configuration for QMD.
4.  Develop the `Log-To-Tier1` AgentSkill.
5.  Define the initial schema for the Tier 3 SQLite database.
6.  Develop the `Consolidate-Tier2` AgentSkill.
