# App Click-through Rate (CTR)

**Platform:** DataLemur
**Company:** Facebook
**Difficulty:** Easy
**Topic:** Aggregation, CASE WHEN, GROUP BY

## Problem

Calculate the click-through rate (CTR) for each app in 2022. CTR is defined as:

> CTR = (Number of Clicks / Number of Impressions) × 100

Return the result rounded to two decimal places.

---

## Approach

* Filter records from the year 2022.
* Group data by `app_id`.
* Count clicks and impressions separately using `CASE WHEN`.
* Calculate the CTR as `(clicks / impressions) × 100`.
* Round the result to 2 decimal places.

---

## SQL Solution

```sql
SELECT
    app_id,
    ROUND(
        100.0 *
        SUM(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) /
        SUM(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END),
        2
    ) AS ctr_rate
FROM events
WHERE timestamp >= '2022-01-01'
  AND timestamp < '2023-01-01'
GROUP BY app_id;
```
## Alternative Approaches

-  `SUM(CASE WHEN ...)` (All SQL Database)
-  `COUNT(CASE WHEN ...)` (All SQL Database)
-  `COUNT(*) FILTER (WHERE ...)` (PostgreSQL)

---

## Concepts Practiced

* Conditional Aggregation
* CASE WHEN
* SUM()
* GROUP BY
* Date Filtering
* ROUND()
