# Post-Remediation Validation Queries

## 1. Document Count Match

GET datasets_curated/_count

---

## 2. Native Revenue Aggregation

POST datasets_curated/_search
{
  "size": 0,
  "aggs": {
    "total_revenue": { "sum": { "field": "revenue" } },
    "avg_revenue": { "avg": { "field": "revenue" } }
  }
}

---

## 3. Quality Flag Distribution

POST datasets_curated/_search
{
  "size": 0,
  "aggs": {
    "flags": {
      "terms": {
        "field": "data_quality_flag.keyword"
      }
    }
  }
}
