# CSV Export Scalability Improvement

## Incident Summary

The reporting module's CSV export functionality was redesigned to support large datasets by processing reports in daily chunks instead of generating the complete export in a single request.

The new implementation introduced chunk-based export generation together with a real-time progress indicator, allowing users to export large reporting windows while providing visibility into the export process.

---

## Background

The CMS reporting dashboard allows administrators to export analytics data as CSV for offline analysis and reporting.

Initially, the export endpoint generated the complete CSV in a single backend request regardless of the selected reporting period.

This approach worked correctly for small and moderate datasets but became increasingly unreliable as the amount of event data grew.

---

## Problem

When users attempted to export reports covering large time ranges, the export frequently failed or became unresponsive.

The implementation attempted to:

* Query the complete reporting period
* Aggregate all records
* Generate the CSV
* Return the entire file as a single response

As production data increased, this approach no longer scaled well.

---

## Root Cause

The export pipeline treated the requested reporting period as a single workload.

Large date ranges resulted in:

* Long-running database queries
* Increased server processing time
* Large HTTP responses
* Poor user feedback during export
* Higher probability of request failure for large datasets

Although database-side aggregation reduced processing costs, exporting the complete dataset in one operation remained a scalability bottleneck.

---

## Design Goals

The revised implementation aimed to:

* Support significantly larger exports
* Reduce workload per request
* Improve perceived responsiveness
* Preserve the existing reporting format
* Avoid major backend architectural changes

---

## Solution

Instead of exporting the entire reporting window in a single request, the export process was divided into daily chunks.

The frontend became responsible for orchestrating the export process.

For each day within the selected reporting range:

1. Request one day's aggregated report.
2. Receive CSV rows from the backend.
3. Append the rows locally.
4. Continue until all days have been processed.
5. Generate a single downloadable CSV.

This distributes the workload across multiple smaller requests while preserving the final output format.

---

## Frontend Changes

The reporting page was updated to:

* Introduce a dedicated CSV export state.
* Divide the selected date range into one-day intervals.
* Sequentially request each interval.
* Merge all returned CSV rows.
* Generate the final downloadable CSV in the browser.
* Display export progress.
* Display the date currently being processed.

These changes significantly improved the user experience during long-running exports.

---

## Backend Changes

The export endpoint was modified to better support chunked processing.

Key changes included:

* Returning only CSV data rows instead of a complete downloadable file.
* Preserving database-side aggregation.
* Allowing multiple responses to be concatenated into a single CSV.
* Maintaining existing filtering and aggregation behaviour.

The backend remained responsible for data aggregation while the frontend coordinated the export workflow.

---

## Benefits

The revised implementation provided several improvements:

* Better scalability for large reporting periods.
* Smaller database workloads per request.
* Reduced likelihood of request failures.
* Continuous progress feedback during export.
* No changes required to the CSV format consumed by users.

---

## Trade-offs

The solution increases the number of HTTP requests during export.

However, this trade-off was considered acceptable because:

* Individual requests complete much faster.
* Failures affect only a small portion of the export.
* Users receive continuous visual feedback.
* Overall reliability improves significantly for large datasets.

---

## Lessons Learned

Long-running reporting operations should not assume that datasets will remain small.

Breaking large workloads into smaller independent units improves both system reliability and user experience while requiring relatively small architectural changes.

Whenever possible, expensive reporting operations should be designed to scale incrementally rather than processing the entire workload as a single request.

---

## Classification

* Reporting
* Scalability
* Performance
* Frontend–Backend Coordination
* Production Improvement
