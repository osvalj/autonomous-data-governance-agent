# Autonomous Data Governance Agent (ADGA)

A multi-step **Elastic Agent Builder** project that audits Elasticsearch operational datasets, generates a governance remediation plan, executes safe/privileged actions when authorized, and validates improvements with measurable before/after metrics.

---

## Problem

Operational analytics often becomes unreliable due to:

- **Schema drift / wrong mappings** (e.g., revenue stored as text → blocks SUM/AVG)
- **Missing identifiers** (e.g., customer_id/dataset_id null rates)
- **Silent segment corruption** (issues concentrated in specific country/category segments)
- **No data quality visibility** (teams can’t reliably filter “good” data)

This leads to broken dashboards, incorrect decisions, and risky manual fixes in production.

---

## What it does (multi-step workflow)

**1) Baseline Audit**
- Mapping and schema checks (blocking vs non-blocking issues)
- Null-rate analysis by field
- Segment-level corruption detection (e.g., by country, product_category)
- Health scoring (status: BLOCKED / UNBLOCKED)

**2) Remediation Plan**
- Prioritized remediation actions
- Risk classification:
  - **SAFE**: non-destructive workarounds (runtime fields, filtered analytics)
  - **PRIVILEGED**: curated reindex, alias swap, ingest pipeline validation

**3) Controlled Execution (permission-aware)**
- If tools are read-only: generates a ready-to-run privileged playbook for Kibana Dev Tools / REST
- If write access is available: creates a curated analytics layer (`datasets_curated`) via:
  - corrected mappings
  - revenue conversion to numeric
  - per-record `data_quality_flag`
  - optional alias `datasets_analytics`

**4) Post-Remediation Validation**
- Document count match
- Native revenue aggregations (no runtime mappings)
- Quality flag distribution
- Updated governance status + execution log

**5) Monitoring Baseline**
- Suggested daily/weekly audit cadence
- Alert thresholds for null rates, health score drops, invalid revenue

---

## Elastic technologies used

- **Elastic Agent Builder** (custom multi-step agent + tool-driven execution)
- **Elasticsearch** indices:
  - `datasets_raw` (raw layer, never modified)
  - `datasets_curated` (curated analytics layer)
- **Kibana Dev Tools** (privileged execution playbook when write access is needed)

---

## Evidence (from this run)

### Population preserved
- `datasets_curated` count: **104**

### Revenue aggregations enabled (schema unblocked)
Global aggregations (native numeric):
- `SUM(revenue)` = **36,703.96**
- `AVG(revenue)` = **495.9995**

### Full data quality visibility
All records are explicitly classified via `data_quality_flag`.

Example distribution:
- `missing_customer_id|missing_dataset_id`: 41  
- `missing_dataset_id`: 33  
- `invalid_revenue|missing_dataset_id`: 20  
- `invalid_revenue|missing_customer_id|missing_dataset_id`: 9  
- `missing_revenue|missing_customer_id`: 1  

Final governance status: **UNBLOCKED (WITH DATA QUALITY WARNINGS)**  
Rationale: analytics is no longer structurally blocked; integrity issues remain but are visible, filterable, and monitorable.

---

## How to test (for judges)

### Prerequisites
- Elasticsearch + Kibana
- Synthetic or open-source dataset (no PII) loaded as `datasets_raw`
- Optional: write access to execute privileged remediation steps

### Option A — Read-only environment (generate playbook + validate baseline)
1. Run the agent baseline audit prompt (see [`agent/instructions.md`](agent/instructions.md))
2. Review generated remediation plan + privileged playbook
3. Run SAFE queries to get immediate usable analytics

### Option B — With write access (full remediation + validation)
1. Execute the privileged playbook in Kibana Dev Tools:
   - create `datasets_curated`
   - reindex with transformation + `data_quality_flag`
   - (optional) create alias `datasets_analytics`
2. Run post-remediation validation queries:
   - document count
   - revenue aggregations
   - flags distribution

Expected outcome:
- `datasets_curated` contains same number of docs as `datasets_raw`
- Revenue aggregations work natively
- Every record has a `data_quality_flag`
- Final status becomes **UNBLOCKED** (with quality warnings if integrity issues persist)

---

## Repository contents

- `agent/instructions.md`: agent configuration and prompts
- `queries/`: baseline, remediation, and validation queries
- `scripts/privileged_playbook_kibana_devtools.ndjson`: copy/paste playbook for Kibana Dev Tools
- `artifacts/`: baseline report, post-remediation report, and before/after metrics

---

## What we liked / challenges

**Liked**
- Multi-step orchestration for a real operational governance workflow
- Clear separation between raw and curated layers (safe-by-design)
- Tool-driven execution with measurable validation outputs

**Challenges**
- Permission-aware remediation (read-only tools vs write operations)
- Converting real-world “messy” fields to strongly typed analytics fields safely

---

## Demo video

Add your demo link here:

- `<VIDEO LINK>`

Video requirements:
- < 3 minutes
- shows the project functioning
- no copyrighted music, third-party trademarks, or personal data without permission

---

## License

MIT (see `LICENSE`).
