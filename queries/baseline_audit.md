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

# Baseline Audit Queries (datasets_raw)

> Run in Kibana Dev Tools. READ-ONLY.

## 1) Count
```http
GET datasets_raw/_count
