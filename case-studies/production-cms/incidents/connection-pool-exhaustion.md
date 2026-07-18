# Connection Pool Exhaustion

## Incident Summary

| Item              | Details                                                                                |
| ----------------- | -------------------------------------------------------------------------------------- |
| Category          | Database Connectivity                                                                  |
| Severity          | High                                                                                   |
| Production Impact | Increased API latency and intermittent database connection failures                    |
| Root Cause        | Database connections remained occupied longer than expected under concurrent workloads |
| Resolution        | Optimized queries, reduced connection hold time, and tuned connection pool usage       |
| Downtime          | Partial service degradation                                                            |

---

## Background

The backend communicated with PostgreSQL through Prisma ORM, which internally manages a pool of database connections.

Instead of creating a new database connection for every incoming request, the application reused a limited number of existing connections. This significantly reduced the overhead of establishing new database sessions and improved overall performance.

During the early stages of the platform, the available connection pool was more than sufficient.

As the number of concurrent users, administrators, and background operations increased, however, the demand for database connections also grew.

---

## The Problem

Under normal operating conditions, requests acquired a database connection, executed one or more queries, and released the connection back to the pool.

As production traffic increased, a different pattern began to emerge.

Some requests required noticeably longer to complete because they executed expensive database queries or generated large reports.

While these requests were running, their database connections remained occupied.

As more requests arrived, the number of available connections gradually decreased until new requests had to wait for an existing connection to become available.

From the application's perspective, this appeared as:

* Increasing API response times
* Requests waiting before executing queries
* Intermittent timeout errors
* Reduced responsiveness during periods of high activity

Although PostgreSQL itself remained operational, the application struggled to obtain database connections quickly enough.

---

## Investigation

The first assumption was that PostgreSQL had become overloaded.

However, resource monitoring told a different story.

CPU and memory utilization were elevated but not fully exhausted.

Further investigation shifted attention toward application behavior instead of server capacity.

Several observations became apparent:

* Multiple expensive queries were executing concurrently.
* Administrative reporting endpoints held database connections for extended periods.
* Large result sets required additional processing before responses could be returned.
* Connection usage increased significantly during reporting and analytics operations.

Rather than indicating a database failure, the evidence suggested that database connections were simply remaining occupied for too long.

---

## Root Cause

The incident was not caused by having too few database connections alone.

Instead, it resulted from connections being retained for longer than necessary.

Several contributing factors included:

* Expensive SQL queries
* Large report generation
* High concurrent request volume
* Increasing database response times as tables grew
* Simultaneous read-heavy and write-heavy workloads

As individual requests required more time to complete, connections were returned to the pool more slowly.

Eventually, demand temporarily exceeded the rate at which connections became available.

---

## Choosing the Solution

Increasing the maximum number of database connections would only postpone the problem if inefficient queries remained unchanged.

The engineering goal therefore became reducing the amount of time each request occupied a database connection.

The optimization effort focused on improving efficiency rather than simply increasing capacity.

---

## Implementation

Several improvements were introduced over time.

### Query Optimization

Frequently executed queries were reviewed to eliminate unnecessary work and reduce execution time.

---

### Database Indexing

Indexes were introduced for commonly filtered columns, allowing PostgreSQL to locate records more efficiently.

The timestamp index on the `EventLog` table was one example of this approach.

---

### Reporting Improvements

Administrative reports were optimized to reduce the amount of data retrieved and processed during a single request.

Long-running reporting operations were reviewed carefully because they tended to occupy database connections for extended periods.

---

### Connection Pool Review

Application configuration was reviewed to ensure the connection pool size aligned with production workloads.

Connection usage was monitored more closely so that future bottlenecks could be detected earlier.

---

## Results

After these improvements:

* Database connections were released more quickly.
* API response times became more consistent.
* Peak traffic was handled more reliably.
* Administrative operations placed less pressure on the connection pool.
* Overall database utilization became more predictable.

Rather than relying solely on increasing connection limits, improving query efficiency produced a more sustainable long-term solution.

---

## Lessons Learned

### A Larger Pool Is Not Always the Answer

Increasing the number of database connections can temporarily mask performance problems but does not eliminate inefficient queries.

The objective should always be reducing connection usage before increasing capacity.

---

### Query Performance Directly Affects Connection Availability

Every additional second spent executing a query is another second that a database connection remains unavailable for other requests.

Improving query performance therefore improves overall application throughput.

---

### Reports Deserve Special Attention

Reporting endpoints often behave differently from transactional APIs.

Large analytical queries can consume database resources for significantly longer than standard CRUD operations and should be monitored independently.

---

### Measure Before Scaling

Infrastructure upgrades should follow measurement, not assumptions.

Understanding why connections remain occupied is often more valuable than simply increasing resource limits.

---

## Takeaway

Connection pool exhaustion is often a symptom rather than the underlying problem.

In this case, the database itself was capable of handling the workload. The real challenge was ensuring that connections were used efficiently and returned to the pool as quickly as possible.

The incident reinforced an important production engineering principle:

> Optimizing database queries not only makes individual requests faster—it allows the entire system to serve more concurrent users with the same infrastructure.
