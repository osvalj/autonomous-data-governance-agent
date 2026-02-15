# Baseline Governance Report

Health Score: 52/100  
Status: BLOCKED

Critical Findings:

- Revenue mapped as text
- 49% null customer_id
- 99% null dataset_id
- Single batch ingestion
- No validation pipeline

Operational Risk:

Analytics decisions currently exposed to unreliable data.

Immediate Safe Recovery:

- Runtime revenue field
- Filtered analytics subset
