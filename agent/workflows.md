# AOAIA Workflows

This document defines the operational execution logic of the Autonomous Operational Analytics Integrity Agent (AOAIA).

The agent follows a deterministic, multi-step governance workflow to protect operational analytics integrity.

---

## Workflow Overview

1. Index Inspection
2. Dataset Overview
3. Advanced Data Diagnostics
4. Health Score Computation
5. Safe Autopilot (if BLOCKED)
6. Privileged Remediation Playbook (if WRITE_ENABLED)
7. Post-Remediation Validation
8. Continuous Monitoring Mode

---

## Execution Modes

### READ_ONLY Mode
- Performs full audit
- Generates safe recovery strategies
- Produces remediation playbook
- Does NOT modify indices

### WRITE_ENABLED Mode
- Executes curated index creation
- Runs reindex transformation
- Applies data quality flags
- Optionally swaps alias
- Creates ingest validation pipeline

---

## Governance Philosophy

AOAIA separates:

- Raw ingestion layer (`datasets_raw`)
- Curated analytics layer (`datasets_curated`)
- Analytics alias (`datasets_analytics`)

Raw data is never modified.

---

## Health Score Logic

Four equal-weight dimensions (25 each):

- Structure
- Integrity
- Usefulness
- Risk

Final score determines dataset state:

- 85–100 → CLEAN
- 60–84 → DEGRADED
- <60 → BLOCKED

---

## Monitoring Strategy

After baseline is established:

- Detect score degradation >10 points
- Detect null increase >5%
- Detect invalid value increase >3%

Monitoring Status:

- STABLE
- DEGRADED
- CRITICAL
