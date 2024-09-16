# ![GA](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png) SQL

---

## Materials We Provide

| Topic | Description | Link |
| --- | --- | --- |
| Slides | Lecture Slides | [Link](./sql.pdf) |
| Notes | Introduction to SQL | [Link](./instructor-notes.md) |
| Archived Material | Alternative material future instructors may find useful | [Link](./archive) |

---

## Startup Instructions
The entirety of this lesson will be carried out in Google Cloud Platform's (GCP's) BigQuery dashboard. To get here, you should:

* Log in with your Google account [here](https://console.cloud.google.com/welcome).
* Navigate to BigQuery, agreeing to any terms of service messages you may encounter. You will NOT need to enter a credit card for any of these steps. No part of this lesson costs money.
* Once you've navigated to BigQuery, create a new SQL query and enter the following:

```sql
SELECT * FROM `bigquery-public-data.ncaa_basketball.mascots` LIMIT 5
```

* We'll be using the NCAA basketball demo dataset provided for free by Google. That is, all tables queried will have the prefix `bigquery-public-data.ncaa_basketball.[...]`

---

## Learning Objectives

After this lessons, students will be able to:

**SQL I:**
- Describe relational databases
- Implement SQL commands:
    - Basic `SELECT`s
    - `LIMIT`
    - Aliasing with `AS`
    - Simple aggregating with `SUM()`, `COUNT()`, etc
    - `DISTINCT`
    - Sorting with `ORDER BY`

**SQL II:**
- Viewing table schemas in BigQuery
- Continue exploring SQL commands with:
    - Filtering with `WHERE`
    - Basic pattern matching with `LIKE` and `regexp_contains()`
    - Viewing and finding `NULL`s
    - `JOIN`s
    - `GROUP BY`s and `HAVING`

**SQL III:**
- Conclude SQL with some additional and advanced topics:
    - Working with datetimes with `EXTRACT()` and `FORMAT_DATETIME()`
    - `CASE WHEN`
    - `CTE`s
    - Subqueries
    - Window functions and rolling statistics

---

## Additional Resources

For more information on this topic, check out the following resources:

- [SQL Zoo](https://sqlzoo.net/) - Pick a topic, learn its syntax, practice that topic
- [Database Tutorials](http://www.tutorialspoint.com/sql/index.htm)
- [Mode](https://mode.com/sql-tutorial/) - Tutorials/practice on various topics
- [SQLBolt](https://sqlbolt.com/lesson/select_queries_introduction) - Build up from the basics
- [W3Resource](https://www.w3resource.com/sql-exercises/) - Lots of practice with SELECT through JOINs and subqueries
- [W3Schools](https://www.w3schools.com/sql/sql_exercises.asp) - A little more detailed than W3Resource
- [This site](https://www.techbeamers.com/sql-query-questions-answers-for-practice/) - 50 sample SQL interview questions
- [SQL murder mystery game](http://mystery.knightlab.com/)
- [Interactive PostgreSQL Exercises](https://pgexercises.com/)
- [Basic SQL Interview Questions](https://www.jitbit.com/news/181-jitbits-sql-interview-questions/)
- [Medium-Hard Data Analyst SQL Interview Questions](https://quip.com/2gwZArKuWk7W)
- ["Is a Dataframe Just a Table?"](https://plateau-workshop.org/assets/papers-2019/10.pdf)
