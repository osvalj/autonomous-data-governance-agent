# Baseline Audit Queries

## 1. Document Count

GET datasets_raw/_count

---

## 2. Time Coverage

POST datasets_raw/_search
{
  "size": 0,
  "aggs": {
    "min_order_date": { "min": { "field": "order_date" } },
    "max_order_date": { "max": { "field": "order_date" } }
  }
}

---

## 3. Distinct Counts

POST datasets_raw/_search
{
  "size": 0,
  "aggs": {
    "countries": { "cardinality": { "field": "country.keyword" } },
    "categories": { "cardinality": { "field": "product_category.keyword" } },
    "customers": { "cardinality": { "field": "customer_id.keyword" } },
    "orders": { "cardinality": { "field": "order_id.keyword" } }
  }
}

---

## 4. Null Rate Detection (Example)

POST datasets_raw/_search
{
  "size": 0,
  "aggs": {
    "missing_customer_id": {
      "missing": { "field": "customer_id.keyword" }
    }
  }
}

---

## 5. Numeric Distribution

POST datasets_raw/_search
{
  "size": 0,
  "aggs": {
    "revenue_stats": {
      "stats": { "field": "revenue" }
    },
    "revenue_percentiles": {
      "percentiles": {
        "field": "revenue",
        "percents": [95,99]
      }
    }
  }
}

# Baseline Audit Report  
Autonomous Operational Analytics Integrity Agent (AOAIA)

## Target Index
datasets_raw

## Executive Summary

Health Score: 52 / 100  
Status: BLOCKED

The dataset contains structural and integrity failures that prevent reliable operational analytics.

Critical findings:

- Revenue mapped as text → blocks numeric aggregation
- 49% null rate on customer_id
- 99% null rate on dataset_id
- Single batch ingestion pattern
- No validation pipeline

This dataset cannot safely power business KPIs in its current state.

---

## Dataset Overview

Total Documents: 104  
Time Coverage: 2026-01-01 → 2026-01-28  
Distinct Countries: 4  
Distinct Categories: 3  
Distinct Customers: 24  
Distinct Orders: 98  

Single ingestion batch detected (all docs ingested same day).

---

## Structural Issues

| Field | Current Type | Impact |
|--------|--------------|--------|
| revenue | text | Blocks SUM / AVG |
| country | text | Requires .keyword |
| product_category | text | Requires .keyword |
| customer_id | text | Requires .keyword |

Revenue field produces 400 error on aggregation.

---

## Integrity Findings

Missing customer_id: 51 / 104 (49%)  
Missing dataset_id: 103 / 104 (99%)

Corruption concentrated in ES and DE segments.

---

## Risk Assessment

- No ingestion validation pipeline
- No quarantine strategy
- No curated analytics layer
- High segment-level corruption concentration

---

## Health Score Breakdown

Structure: 12 / 25  
Integrity: 10 / 25  
Usefulness: 15 / 25  
Risk: 15 / 25  

Total: 52 / 100  

Status: BLOCKED

---

Baseline established.

