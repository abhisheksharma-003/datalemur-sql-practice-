# SQL Interview Preparation

A curated collection of SQL interview problems solved using **PostgreSQL**. This repository documents my SQL learning journey through **DataLemur**, covering fundamental to advanced SQL concepts commonly tested in Data Analyst, Data Engineer, and Analytics Engineering interviews.

> **Note:** Problem statements belong to DataLemur. Each problem title links to the original question, while this repository contains my own solutions, approaches, and learning notes.

---

## Repository Structure

```text
.
├── Easy/
├── Medium/
├── Hard/
└── README.md
```

---

## Progress

| Difficulty | Solved |
| ---------- | :----: |
| Easy       |    14   |
| Medium     |    0   |
| Hard       |    0   |
| **Total**  |  **14** |

---

## SQL Concepts Covered

* SELECT
* WHERE
* ORDER BY
* GROUP BY
* HAVING
* Aggregate Functions
* CASE WHEN
* JOINS
* Subqueries
* Common Table Expressions (CTEs)
* Window Functions
* Date Functions
* String Functions
* Set Operations
* Ranking Functions

---

## Problem Index

| #   | Platform  | Problem                                                                                   | Difficulty | Company   | Concepts                                                                                  | Solution                                   |
| --- | --------- | ----------------------------------------------------------------------------------------- | ---------- | --------- | ----------------------------------------------------------------------------------------- | ------------------------------------------ |
| 001 | DataLemur | [App Click-through Rate](https://datalemur.com/questions/click-through-rate)              | Easy       | Facebook  | Aggregation, CASE WHEN, GROUP BY                                                          | [View](Easy/001_app_click_through_rate.md) |
| 002 | DataLemur | [Page With No Likes](https://datalemur.com/questions/sql-page-with-no-likes)              | Easy       | Facebook  | Anti Join, LEFT JOIN, IS NULL, NOT EXISTS, EXCEPT, NOT IN, ORDER BY                       | [View](Easy/002_pages_with_no_likes.md)    |
| 003 | DataLemur | [Average Post Hiatus (Part 1)](https://datalemur.com/questions/sql-average-post-hiatus-1) | Easy       | Facebook  | GROUP BY, MIN/MAX, Date Functions                                                         | [View](Easy/003_average_post_hiatus_1.md)  |
| 004 | DataLemur | [Histogram of Tweets](https://datalemur.com/questions/sql-histogram-tweets)               | Easy       | Twitter   | Subqueries, Derived Tables, CTEs, Multi-Level Aggregation, GROUP BY, Date Range Filtering | [View](Easy/004_histogram_of_tweets.md)    |
| 005 | DataLemur | [Data Science Skills](https://datalemur.com/questions/matching-skills)                    | Easy       | LinkedIn  | GROUP BY, COUNT(DISTINCT), HAVING, WHERE                                                  | [View](Easy/005_data_science_skills.md)    |
| 006 | DataLemur | [Duplicate Job Listings](https://datalemur.com/questions/duplicate-job-listings)          | Easy       | LinkedIn  | CTE, COUNT(), GROUP BY, COUNT(DISTINCT), HAVING, WHERE                                    | [View](Easy/006_duplicate_job_listings.md) |
| 007 | DataLemur | [Average Review Ratings](https://datalemur.com/questions/sql-avg-review-ratings)          | Easy       | Amazon    | AVG(), ROUND(), EXTRACT(), GROUP BY, ORDER BY, Date Functions                             | [View](Easy/007_average_review_ratings.md) |
| 008 | DataLemur | [Teams Power Users](https://datalemur.com/questions/teams-power-users)                    | Easy       | Microsoft | COUNT(), EXTRACT(), GROUP BY, ORDER BY, LIMIT, Top-N Queries                              | [View](Easy/008_teams_power_users.md)      |
| 009 | DataLemur | [Unfinished Parts](https://datalemur.com/questions/tesla-unfinished-parts)                | Easy       | Tesla     | IS NULL, COALESCE()                                                                       | [View](Easy/009_unfinished_parts.md)       |
| 010 | DataLemur | [Laptop vs. Mobile Viewership](https://datalemur.com/questions/laptop-mobile-viewership)    | Easy       | NY Times  | Conditional Aggregation, FILTER, CASE WHEN, COUNT(), GROUP BY, IN                          | [View](Easy/010_laptop_vs_mobile_viewership.md) |
| 011 | DataLemur | [Cities With Completed Trades](https://datalemur.com/questions/completed-trades) | Easy | Robinhood | Aggregate Functions, GROUP BY, COUNT(), ORDER BY, LIMIT, Top-N Queries | [View](Easy/011_cities_with_completed_trades.md) |
| 012 | DataLemur | [Well Paid Employees](https://datalemur.com/questions/sql-well-paid-employees) | Easy | FAANG | Self Join, INNER JOIN, Table Aliases, WHERE, Comparison Operators, Employee–Manager Relationships | [View](Easy/012_well_paid_employess.md) |
| 013 | DataLemur | [Final Account Balance](https://datalemur.com/questions/final-account-balance) | Easy | PayPal | Aggregate Functions, CASE WHEN, SUM(), GROUP BY, Conditional Aggregation, Positive/Negative Value Transformation | [View](Easy/013_final_account_balance.md) | 
| 014 | DataLemur | [Second Day Confirmation](https://datalemur.com/questions/second-day-confirmation) | Easy | TikTok | INNER JOIN, Date Arithmetic, INTERVAL, WHERE, Equality Comparison, Filtering, Date Functions | [View](Easy/014_second_day_confirmation.md) |
| 015 | DataLemur | [IBM db2 Product Analytics](https://datalemur.com/questions/sql-ibm-db2-product-analytics) | Easy | IBM | LEFT JOIN, COALESCE, GROUP BY, DISTINCT, Date Range Filtering, Histogram | [View](Easy/015_IBM_db2_product_analytics.md) |
| 016 | DataLemur | [Cards Issued Difference](https://datalemur.com/questions/cards-issued-difference) | Easy | JPMorgan Chase | Aggregate Functions, MAX(), MIN(), Arithmetic Operations, GROUP BY, ORDER BY | [View](Easy/016_cards_issued_difference.md) |
| 017 | DataLemur | [Compressed Mean](https://datalemur.com/questions/alibaba-compressed-mean) | Easy | Alibaba | Aggregate Functions, Weighted Average, Type Casting, Integer vs Decimal Division, Arithmetic Expressions, ROUND() | [View](Easy/017_compressed_mean.md) |


---

## SQL Patterns

This repository covers common SQL interview patterns including:

* Aggregation
* Conditional Aggregation
* Filtering
* Sorting
* Joins
* Anti Joins
* Subqueries
* Common Table Expressions (CTEs)
* Window Functions
* Ranking
* Running Totals
* Date Manipulation
* Set Operations

---

## Tech Stack

* PostgreSQL
* DataLemur
* Git & GitHub

---

## License

This repository is intended for educational purposes. All problem statements and original questions belong to **DataLemur**. Only my own SQL solutions and explanations are included here.
