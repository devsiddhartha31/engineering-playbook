# PM2 Process Management

## Incident Summary

| Item               | Details                                                                                                    |
| ------------------ | ---------------------------------------------------------------------------------------------------------- |
| Category           | Application Operations                                                                                     |
| Approximate Period | Early production deployment                                                                                |
| Severity           | Medium                                                                                                     |
| Production Impact  | Risk of application downtime due to process termination or server restarts                                 |
| Root Cause         | Node.js application was initially running as a regular process without production-grade process management |
| Resolution         | Adopted PM2 for process management, monitoring, and automatic recovery                                     |
| Downtime           | None during migration                                                                                      |

---

## Environment

| Component        | Technology   |
| ---------------- | ------------ |
| Operating System | Ubuntu       |
| Cloud Provider   | DigitalOcean |
| Runtime          | Node.js      |
| Process Manager  | PM2          |
| Reverse Proxy    | Nginx        |

---

## Background

During the early stages of development, the backend application was started manually using standard Node.js commands.

This approach was sufficient while the application was under active development and deployments were performed manually.

However, production systems have different operational requirements.

A production application is expected to:

* Continue running continuously.
* Recover automatically after unexpected failures.
* Start automatically after server reboots.
* Support zero or minimal downtime deployments.
* Provide operational visibility into running processes.

Running the application manually no longer satisfied these requirements as the platform matured.

---

## Incident Detection

Although the application itself remained stable, several operational risks became apparent.

If the Node.js process terminated unexpectedly, the backend would become unavailable until someone manually restarted it.

Similarly, following a server reboot, the application required manual intervention before becoming operational again.

These limitations introduced unnecessary operational risk and made the deployment process increasingly dependent on manual actions.

Rather than waiting for a production outage, the decision was made to introduce proper process management.

---

## Investigation

The engineering objective was identifying a lightweight process manager capable of:

* Automatically restarting crashed processes.
* Starting applications during server boot.
* Managing environment variables.
* Simplifying deployments.
* Providing process monitoring.

PM2 was selected because it addressed these operational requirements while integrating naturally with the existing Node.js application.

---

## Root Cause

The issue was not an unstable application.

The real limitation was relying on a manually managed Node.js process.

Without a process manager:

* Unexpected crashes required manual recovery.
* Server restarts interrupted application availability.
* Process monitoring was limited.
* Deployments required additional manual effort.

As the platform became more important, these operational limitations became unacceptable.

---

## Resolution

PM2 was introduced as the production process manager.

Application startup changed from running Node.js directly to being managed by PM2.

Example:

```bash
pm2 start ecosystem.config.js
```

The application was configured to:

* Restart automatically after unexpected failures.
* Launch automatically when the server rebooted.
* Maintain environment-specific configuration.
* Support controlled restarts during deployments.

Startup persistence was configured using:

```bash
pm2 startup
pm2 save
```

This ensured that application processes were automatically restored whenever the server restarted.

---

## Long-Term Improvements

Once PM2 became part of the production infrastructure, several operational practices were standardized.

These included:

* Monitoring process health using PM2.
* Restarting services without affecting server configuration.
* Managing environment-specific deployments.
* Simplifying application updates.
* Reviewing application logs through PM2.

The deployment workflow also became more predictable because process management was no longer handled manually.

---

## Commands Used During Investigation

### View Running Processes

```bash
pm2 list
```

Displays all managed applications, their status, uptime, restart count, CPU usage, and memory consumption.

---

### View Detailed Process Information

```bash
pm2 show <application-name>
```

Provides detailed runtime information for an individual application.

---

### Monitor Live Resource Usage

```bash
pm2 monit
```

Displays real-time CPU usage, memory consumption, and application logs.

---

### Restart an Application

```bash
pm2 restart <application-name>
```

Restarts a managed application without requiring manual process management.

---

### Persist PM2 Configuration

```bash
pm2 startup
pm2 save
```

Ensures applications automatically restart after system reboots.

---

## Results

After adopting PM2:

* Applications automatically recovered from unexpected failures.
* Server reboots no longer required manual intervention.
* Deployments became simpler and more consistent.
* Operational visibility improved significantly.
* Application uptime increased.

Rather than relying on engineers to manually manage processes, the infrastructure became capable of maintaining application availability automatically.

---

## Lessons Learned

### Production Applications Need Process Managers

Running a production application directly with `node` is suitable for development but introduces unnecessary operational risk in production.

---

### Automation Improves Reliability

Every manual operational step represents an opportunity for human error.

Automating process recovery and startup significantly improves production stability.

---

### Operational Tooling Is Part of the Architecture

Infrastructure is more than servers and databases.

Tools such as PM2 play an important role in maintaining reliable production systems.

---

### Plan for Failure

Processes eventually terminate—whether due to software defects, infrastructure issues, or planned maintenance.

Production systems should be designed to recover automatically whenever possible.

---

## Takeaway

One of the simplest infrastructure improvements made during the platform's evolution was introducing a dedicated process manager.

PM2 transformed the backend from a manually operated Node.js application into a self-managing production service capable of automatic recovery, simplified deployments, and improved operational reliability.

Although the application code remained largely unchanged, the operational maturity of the platform increased significantly.

---

## Related Documents

* [Infrastructure Evolution](../06-infrastructure-evolution.md)
* [DigitalOcean Droplet Upgrade](digitalocean-droplet-upgrade.md)
* [Nginx Log Disk Exhaustion](nginx-log-disk-exhaustion.md)
* [Connection Pool Exhaustion](connection-pool-exhaustion.md)
