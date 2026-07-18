# Report Query Optimization

## Incident Summary

| Item               | Details                                                                                      |
| ------------------ | -------------------------------------------------------------------------------------------- |
| Category           | Database Performance                                                                         |
| Approximate Period | As reporting requirements expanded                                                           |
| Severity           | High                                                                                         |
| Production Impact  | Administrative reports became increasingly slow as the dataset grew                          |
| Root Cause         | Reporting queries scanned large datasets and retrieved more data than necessary              |
| Resolution         | Optimized SQL queries, reduced unnecessary data retrieval, and introduced supporting indexes |
| Downtime           | None                                                                                         |

---

## Environment

| Component        | Technology         |
| ---------------- | ------------------ |
| Database         | PostgreSQL         |
| ORM              | Prisma             |
| Backend          | Node.js            |
| Reporting Module | Administrative CMS |

---

## Background

The CMS included several administrative reports that allowed operators to analyze platform activity over specific periods.

Typical reports included:

* User activity
* Event statistics
* Daily and monthly summaries
* Exportable datasets
* Operational analytics

During the early stages of development, these reports executed almost instantly because the underlying tables contained relatively little data.

As the platform expanded, however, the amount of historical information increased dramatically.

Queries that once processed thousands of rows eventually needed to process millions.

---

## Incident Detection

The first indication of the problem came from administrative users.

Reports that previously loaded in seconds gradually became slower.

The degradation was not immediate—it appeared progressively as the amount of stored data increased.

The issue became particularly noticeable when generating reports covering longer time ranges.

Although transactional APIs remained responsive, reporting endpoints required significantly more time to complete.

---

## Investigation

The investigation focused on understanding why reporting operations behaved differently from normal application requests.

Several observations emerged:

* Reports processed significantly larger datasets.
* Most reports filtered records by date ranges.
* Some queries returned more columns than were actually displayed.
* Certain aggregations required PostgreSQL to scan large portions of the underlying tables.
* Export operations transferred large result sets from the database to the application.

The problem was not caused by a single inefficient query.

Instead, it resulted from several small inefficiencies becoming increasingly expensive as the dataset grew.

---

## Root Cause

The reporting module had been designed when the database contained relatively little historical data.

As the platform matured:

* Tables grew substantially.
* Date-range searches became more expensive.
* Larger result sets required more processing.
* Database connections remained occupied for longer periods.
* Application memory usage increased during report generation.

The queries themselves remained logically correct.

The challenge was that they no longer matched the scale of the production dataset.

---

## Resolution

Several improvements were implemented over time.

### Index Frequently Filtered Columns

Columns commonly used in reporting filters—particularly timestamps—were indexed to reduce the number of rows PostgreSQL needed to examine.

---

### Retrieve Only Required Data

Queries were reviewed to ensure they selected only the columns required by the report.

Reducing unnecessary data transfer lowered both database workload and application memory usage.

---

### Optimize Aggregations

Aggregation queries were rewritten where appropriate to reduce unnecessary computation and improve execution efficiency.

---

### Review Query Execution Patterns

Reporting queries were analyzed individually rather than assuming all reports required identical optimization strategies.

Each report was optimized according to its actual access pattern.

---

## Results

Following these improvements:

* Reports completed more quickly.
* Database workload during reporting decreased.
* Administrative users experienced faster response times.
* Database connections were released sooner.
* Overall application responsiveness improved during reporting operations.

Most importantly, the reporting module continued performing reliably even as historical data continued growing.

---

## Commands Used During Investigation

### Analyze Query Execution

```sql
EXPLAIN ANALYZE
SELECT ...
```

Used to understand query execution plans and identify expensive operations such as sequential scans and costly aggregations.

---

### Review Existing Indexes

```sql
\d "EventLog"
```

Verified available indexes on frequently queried columns.

---

### Monitor Long-Running Queries

```sql
SELECT pid,
       state,
       query,
       query_start
FROM pg_stat_activity;
```

Helped identify queries occupying database connections for extended periods.

---

### Create Supporting Indexes

```sql
CREATE INDEX CONCURRENTLY idx_eventlog_timestamp
ON "EventLog" ("timestamp");
```

Reduced the cost of frequently executed time-based reporting queries.

---

## Lessons Learned

### Reporting Is Different from CRUD

Reporting workloads often require scanning and aggregating significantly more data than transactional APIs.

They should be designed and optimized independently.

---

### Optimize for Real Usage Patterns

Understanding how administrators actually use reports is more valuable than prematurely optimizing every query.

Indexes and query improvements should reflect real production access patterns.

---

### Small Inefficiencies Grow with Data

Selecting unnecessary columns or scanning additional rows may have little impact on small datasets.

At production scale, these inefficiencies become increasingly expensive.

---

### Measure Before Optimizing

Database optimization should be driven by execution plans, performance metrics, and observed production behavior—not assumptions.

---

## Takeaway

Reporting systems naturally evolve as applications grow.

Queries that perform well during early development may require substantial optimization once they begin operating against millions of production records.

By analyzing real query patterns, introducing targeted indexes, and reducing unnecessary work, the reporting module continued to scale without requiring major architectural changes.

---

## Related Documents

* [EventLog Timestamp Indexing](eventlog-indexing.md)
* [Connection Pool Exhaustion](connection-pool-exhaustion.md)
* [Database Optimization](../04-database-optimization.md)
* [Infrastructure Evolution](../06-infrastructure-evolution.md)
