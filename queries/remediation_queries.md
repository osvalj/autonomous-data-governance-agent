# Remediation Support Queries

## Runtime Field Workaround (SAFE)

GET datasets_raw/_search
{
  "runtime_mappings": {
    "revenue_numeric": {
      "type": "double",
      "script": "if (doc['revenue.keyword'].size() > 0) emit(Double.parseDouble(doc['revenue.keyword'].value));"
    }
  },
  "aggs": {
    "total_revenue": {
      "sum": { "field": "revenue_numeric" }
    }
  }
}

---

## Filtered Clean Records

POST datasets_raw/_search
{
  "query": {
    "bool": {
      "must": [
        { "exists": { "field": "customer_id" } },
        { "exists": { "field": "revenue" } }
      ]
    }
  }
}
