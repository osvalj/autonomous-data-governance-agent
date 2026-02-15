# Workflows

This repo contains the full workflow used by the Autonomous Operational Analytics Integrity Agent (AOAIA) to audit a raw operational dataset, apply a safe recovery layer, generate a privileged remediation playbook, and validate the curated outcome.

## Workflow 1 — Baseline Audit (READ-ONLY)

Goal: quantify schema and data integrity blockers in `datasets_raw` without modifying any data.

Steps:
1) Index existence + mapping snapshot
2) Dataset overview: volume, time coverage, cardinalities, top segments
3) Data quality diagnostics: nulls, invalid revenue (text -> numeric), segment concentration
4) Health Score (0–100) + status classification (CLEAN / DEGRADED / BLOCKED)
5) Safe Autopilot: runtime-based recovery queries (no writes)

Queries: `queries/baseline_audit.md`  
Output artifact: `artifacts/baseline_report.md`  
Screenshots: `artifacts/screenshots/`

## Workflow 2 — Privileged Remediation (WRITE-ENABLED via Kibana Dev Tools)

Goal: create a curated analytics index that is aggregation-safe and adds deterministic quality flags.

Steps:
1) Create `datasets_curated` with corrected mappings (notably `revenue: double`)
2) Reindex from `datasets_raw` -> `datasets_curated` with transformation script
3) (Optional) Create alias `datasets_analytics` -> `datasets_curated`
4) Create ingest pipeline `datasets_validation` for future protection

Playbook: `scripts/privileged_playbook_kibana_devtools.ndjson`  
Queries: `queries/remediation_queries.md`

## Workflow 3 — Post-Remediation Validation

Goal: prove the remediation worked and quantify before/after improvements.

Validation checks:
- Curated index exists and mapping is correct
- Revenue aggregations succeed natively in `datasets_curated`
- Quality flag distribution is present and consistent
- Before/after metrics table is produced

Queries: `queries/post_validation.md`  
Output artifacts:
- `artifacts/post_remediation_report.md`
- `artifacts/before_after_metrics.md`
- `artifacts/screenshots/`
