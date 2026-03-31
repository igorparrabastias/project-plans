# Plan: 3-Tier Memory Architecture Enhancement (Completed)

Based on the principles outlined in the referenced video (https://www.youtube.com/watch?v=LHoB1C2H3f0).

## Objective
Evolve the agent's memory system to a 3-tier architecture for improved recall, reduced token dependency, and autonomous operation.

## System Constraint Analysis
- **Host:** Google Cloud VM, type `e2-medium` (2 vCPUs, 4 GB RAM).
- **Primary Concern:** **[Mitigated]** The initial concern about memory usage for a local model was mitigated by using the QMD sidecar which relies on API calls for heavy processing.
- **[Update 2026-03-30] Operational RAM Risk:** With QMD installed, a new risk was identified. QMD's search and reranking functions can be memory intensive. More critically, the sub-agent process for consolidation consumes memory. This requires monitoring, and a swap file is recommended as a mitigation strategy.

## Implemented Architecture

### Tier 1: Working Memory (Human/Agent Layer)
- **Technology:** Filesystem-based, compatible with Obsidian.
- **Implementation:**
    -   **[COMPLETED]** Defined and created the directory structure at `~/.openclaw/workspace/memory/`.
    -   **[COMPLETED]** Developed the `log_entry` AgentSkill for consistent, timestamped logging to the `daily/` directory.

### Tier 2: Semantic Memory (Long-Term Recall)
- **Technology:** OpenClaw QMD Sidecar.
- **Implementation:**
    -   **[COMPLETED]** Successfully installed and configured the QMD sidecar to index both Tier 1 and Tier 2 directories.
    -   **[COMPLETED]** The initial installation via `bun` failed due to memory constraints; this was resolved by installing via `npm`.

### Tier 3: Hard Data (Deterministic Storage)
- **Technology:** SQLite.
- **Implementation:**
    -   **[PENDING]** The database will be located at `~/.openclaw/workspace/memory/tier3_structured/facts.sqlite`.
    -   Defining the schema is the next pending task.

## Core Processes

### "Dream Cycle" (Periodic Maintenance)
- **Technology:** Cron job executing a custom Node.js AgentSkill.
- **Implementation:**
    -   **[COMPLETED]** Developed the `consolidate_memory` AgentSkill.
    -   The skill is implemented in Node.js for robust JSON parsing and error handling.
    -   It uses a **batched approach**, processing all eligible log files in a single, efficient API call to a sub-agent to minimize overhead.
    -   **[COMPLETED]** A cron job is scheduled to run this skill nightly at 03:00 local time (America/Santiago).

## Project Status
1.  **[COMPLETED]** Confirm the proposed plan and architecture.
2.  **[COMPLETED]** Select and create the Tier 1 memory store directory structure.
3.  **[COMPLETED]** Implement OpenClaw configuration for QMD.
4.  **[COMPLETED]** Develop the `log_entry` AgentSkill.
5.  **[COMPLETED]** Develop the `consolidate_memory` AgentSkill.
6.  **[COMPLETED]** Optimize the `consolidate_memory` skill for batched processing.
7.  **[COMPLETED]** Schedule the nightly consolidation cron job.

## Next Step
1.  Define the initial schema for the Tier 3 SQLite database.

