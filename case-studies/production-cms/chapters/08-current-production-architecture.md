# 08. Current Production Architecture

## Overview

The platform has evolved considerably from its initial implementation.

What began as a straightforward application has gradually matured into a production system with a stronger emphasis on reliability, scalability, operational efficiency, and maintainability.

Throughout this evolution, improvements were introduced across every layer of the stack, including the backend, database, infrastructure, deployment process, and operational practices.

This chapter summarizes the current production architecture and highlights how the various components interact to support the platform.

---

## High-Level Architecture

The production environment consists of the following primary components:

```text
                    Users
                      │
                      ▼
                  Internet
                      │
                      ▼
                   Nginx
          (Reverse Proxy / SSL)
                      │
                      ▼
               Node.js Backend
              (Express + Prisma)
                      │
          ┌───────────┴───────────┐
          ▼                       ▼
     PostgreSQL              Background Tasks
         Database             (PM2 Managed)
```

Nginx serves as the public entry point, forwarding incoming requests to the backend application while handling reverse proxy responsibilities.

The backend processes business logic, validates requests, and communicates with PostgreSQL through Prisma ORM.

PM2 manages the application processes, ensuring automatic recovery and simplified deployment management.

---

## Application Layer

The backend follows a modular architecture where responsibilities are separated into logical components.

Major responsibilities include:

* API request handling
* Business logic
* Database access
* Authentication and authorization
* Administrative reporting
* File generation and exports

Keeping these responsibilities separated has simplified maintenance and enabled incremental improvements over time.

---

## Database Layer

PostgreSQL serves as the primary data store for the platform.

As production data increased, the database evolved through:

* Improved schema organization
* Targeted indexing
* Query optimization
* Performance tuning for reporting workloads
* Better handling of large historical datasets

Rather than redesigning the database entirely, continuous incremental improvements allowed it to scale alongside the application.

---

## Infrastructure Layer

The production environment currently relies on:

* Ubuntu Server
* DigitalOcean Droplet
* Nginx
* PM2
* PostgreSQL
* Node.js

Infrastructure maintenance now includes routine monitoring of:

* CPU utilization
* Memory usage
* Disk usage
* Application processes
* Log growth

Operational improvements such as log rotation and process management have significantly increased platform stability.

---

## Deployment Workflow

A typical deployment consists of the following stages:

1. Pull the latest application code.
2. Install dependencies.
3. Build the application.
4. Apply any required database migrations.
5. Restart application processes using PM2.
6. Verify application availability.
7. Monitor logs for unexpected errors.

This standardized deployment process improves consistency and reduces operational risk.

---

## Scalability Considerations

Several architectural improvements have prepared the platform for continued growth.

Examples include:

* Database indexing for frequently accessed data.
* Optimized reporting queries.
* Efficient database connection usage.
* Infrastructure upgrades as resource demands increased.
* Automated operational processes.

These improvements allow the system to support larger datasets and higher workloads without requiring major architectural redesigns.

---

## Monitoring and Operations

Operating a production system extends beyond software development.

Routine operational activities include:

* Monitoring application health.
* Reviewing infrastructure metrics.
* Checking disk utilization.
* Verifying successful deployments.
* Maintaining server updates.
* Monitoring application logs.
* Investigating production anomalies.

These practices help identify issues before they affect users.

---

## Current Engineering Principles

The current architecture is guided by several key principles:

* Build simple systems before introducing complexity.
* Optimize based on production evidence.
* Automate repetitive operational tasks.
* Design for maintainability.
* Treat infrastructure as an integral part of the application.
* Document important engineering decisions.

These principles have influenced every stage of the platform's evolution.

---

## Takeaway

The current production architecture reflects continuous improvement rather than a single large redesign.

Each optimization, infrastructure enhancement, and operational refinement contributed incrementally to a more reliable, scalable, and maintainable platform.

Rather than pursuing unnecessary architectural complexity, the system evolved by solving real production problems as they emerged.
