# Nginx Log Disk Exhaustion

## Incident Summary

| Item               | Details                                                                                  |
| ------------------ | ---------------------------------------------------------------------------------------- |
| Category           | Infrastructure                                                                           |
| Approximate Period | During production deployment                                                             |
| Severity           | Critical                                                                                 |
| Production Impact  | Application deployment failed due to exhausted disk space                                |
| Root Cause         | Missing log retention strategy allowed Nginx logs to grow indefinitely                   |
| Resolution         | Reclaimed disk space by truncating oversized logs and implemented automatic log rotation |
| Downtime           | Deployment delayed until storage was recovered                                           |

---

## Environment

| Component        | Technology   |
| ---------------- | ------------ |
| Operating System | Ubuntu       |
| Cloud Provider   | DigitalOcean |
| Reverse Proxy    | Nginx        |
| Process Manager  | PM2          |
| Backend          | Node.js      |
| Database         | PostgreSQL   |

---

## Background

The production environment was hosted on a DigitalOcean droplet running Ubuntu.

The server hosted multiple components, including Nginx, Node.js, PostgreSQL, PM2, and the CMS application.

Like most production web servers, Nginx continuously generated access and error logs.

Initially, these log files remained relatively small and required little attention.

As the platform matured and traffic increased, however, every incoming request generated additional log entries.

Over several months of continuous operation, these log files gradually accumulated into several gigabytes of data.

Because storage usage increased slowly over time, the issue went unnoticed until it eventually affected production operations.

---

## Incident Detection

The incident was first noticed during a routine production deployment.

The deployment unexpectedly failed with the following operating system error:

```text
ENOSPC: no space left on device
```

Since only minor application changes had been introduced, the failure appeared unusual.

The error message originated from the operating system rather than the application itself, indicating that the deployment process was likely not the actual source of the problem.

> **Key Observation**
>
> The deployment pipeline was functioning correctly. The operating system could no longer allocate sufficient disk space for the build process.

This shifted the investigation away from the application code and toward the server infrastructure.

---

## Investigation

The first objective was determining why the operating system reported insufficient storage.

Disk utilization was checked first.

```bash
df -h
```

Output similar to:

```text
Filesystem      Size  Used Avail Use%
/dev/vda1        50G   50G   20M 100%
```

The root filesystem had reached nearly 100% utilization.

The next step was identifying which directories consumed the majority of the available storage.

```bash
sudo du -sh /* 2>/dev/null
```

This narrowed the investigation to the `/var` directory.

Further inspection of the Nginx logs revealed the actual cause.

```bash
ls -lh /var/log/nginx
```

Output similar to:

```text
-rw-r----- 1 www-data adm 5.5G access.log
-rw-r----- 1 www-data adm 6.1G error.log
```

Two Nginx log files alone occupied approximately **11 GB** of storage.

The deployment itself was never the problem.

The operating system had simply exhausted its available disk space.

---

## Root Cause

The underlying issue was not Nginx itself.

The real problem was the absence of a log retention strategy.

Nginx continuously generated access and error logs, but these files were never automatically rotated or cleaned.

Although each individual log entry was extremely small, millions of incoming requests gradually produced log files large enough to consume nearly all available storage on the server.

Eventually, the operating system could no longer allocate additional disk space for deployment artifacts, causing every subsequent deployment to fail.

---

## Resolution

After confirming the source of the issue, disk space was reclaimed immediately by truncating the oversized log files.

```bash
sudo truncate -s 0 /var/log/nginx/access.log
sudo truncate -s 0 /var/log/nginx/error.log
```

### Why `truncate` Instead of `rm`?

Rather than deleting the log files, they were truncated to zero bytes.

This preserved the existing files, ownership, and permissions while immediately reclaiming disk space.

Nginx could continue writing to the same log files without requiring additional recovery steps.

Once storage became available again:

* The deployment completed successfully.
* Build operations resumed normally.
* Application functionality remained unaffected.
* No code changes were required.

The incident was resolved entirely through infrastructure maintenance.

---

## Long-Term Improvements

Although truncating the logs resolved the immediate issue, it did not prevent the problem from occurring again.

To eliminate the root cause, automatic log rotation was configured using `logrotate`.

Example configuration:

```conf
/var/log/nginx/*.log {
    su root adm
    rotate 7
    daily
    compress
    delaycompress
    missingok
    notifempty
    create 0640 www-data adm
    sharedscripts

    postrotate
        systemctl reload nginx > /dev/null 2>&1 || true
    endscript
}
```

This configuration automatically:

* Rotates active log files.
* Compresses archived logs.
* Retains a limited number of historical logs.
* Creates new log files with the correct ownership and permissions.
* Reloads Nginx so logging continues without interruption.

After introducing automated log rotation, log growth became predictable and no longer threatened server storage.

---

## Results

The incident highlighted that infrastructure health is just as important as application health.

Approximately **11 GB** of storage was recovered immediately after truncating the log files.

As a result:

* Production deployments became reliable again.
* The server regained sufficient free disk space.
* No application changes were necessary.
* Storage monitoring became part of routine server maintenance.
* Automatic log rotation prevented the issue from reoccurring.

Perhaps most importantly, the engineering team gained a deeper appreciation for the importance of proactive infrastructure maintenance.

---

## Lessons Learned

### Infrastructure Can Fail Independently of the Application

Not every production issue originates from application code.

Sometimes the operating system itself becomes the bottleneck.

Understanding the surrounding infrastructure is therefore an essential engineering skill.

---

### Logs Require Lifecycle Management

Logs are invaluable for debugging and monitoring, but they are not free.

Without automatic rotation or cleanup, they continue consuming storage indefinitely.

Every production logging strategy should include a retention policy.

---

### Monitor Disk Usage Proactively

CPU, memory, and database metrics often receive significant attention.

Disk utilization deserves the same level of monitoring.

Running out of storage can prevent deployments, interrupt application behavior, and create cascading operational failures.

---

### Read Error Messages Carefully

The deployment failed because of an operating system error—not an application exception.

Recognizing this distinction significantly reduced investigation time and prevented unnecessary debugging of application code.

---

## Takeaway

This incident demonstrated that production reliability extends beyond writing efficient software.

Applications depend on healthy infrastructure, and even something as ordinary as server log files can become a critical operational risk when left unmanaged.

One of the simplest operational improvements—implementing automatic log rotation—eliminated an entire class of deployment failures and became a permanent part of the platform's infrastructure management.

---

## Related Documents

* [Infrastructure Evolution](../chapters/06-infrastructure-evolution.md)
* [EventLog Timestamp Indexing](eventlog-indexing.md)
* [Connection Pool Exhaustion](connection-pool-exhaustion.md)