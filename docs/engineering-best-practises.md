# Engineering Guidelines

This document serves as a checklist of engineering practices that should be followed throughout the lifecycle of a software project.

The goal is consistency, maintainability, and preserving engineering knowledge over time.

---

# 1. Project Documentation

Every project should include a well-maintained `README.md`.

The README should explain:

- Project overview
- Features
- Technology stack
- Architecture overview
- Installation instructions
- Environment setup
- Folder structure
- Screenshots (if applicable)
- Future roadmap

A good README allows a new developer to understand the project without reading the source code first.

Public or open-source repositories should also include a `LICENSE` file that clearly defines how the software may be used, modified, and distributed.

---

# 2. Contributing Guidelines

A CONTRIBUTING.md file provides contributors with clear instructions on how to participate in the project. It helps establish a consistent development workflow, reduces onboarding time, and minimizes common mistakes.

A good CONTRIBUTING.md typically includes:

Prerequisites and project setup instructions
Branching strategy
Coding standards and style guidelines
Commit message conventions
Pull Request process
Testing requirements
Issue reporting guidelines
Code review expectations
Links to additional project documentation

Every contributor should read the contributing guidelines before submitting changes to ensure their work aligns with the project's engineering standards.

For open-source projects, a well-written CONTRIBUTING.md is often one of the most important documents after the README.md, as it defines how the community collaborates and maintains code quality.

---

# 3. Branching Strategy

Avoid pushing directly to the `main` branch.

Recommended workflow:

```text
    Issue
      ↓
Feature Branch
      ↓
 Pull Request
      ↓
 Code Review
      ↓
Merge into Main
```

Benefits include:

* Easier code reviews
* Better commit history
* Safer deployments
* Easier rollbacks

---

# 4. GitHub Issues

Use GitHub Issues to track:

* Bugs
* Features
* Improvements
* Technical debt
* Research tasks

Each issue should describe:

* Problem
* Expected outcome
* Priority
* Additional notes

Issues become the historical record of why work was performed.

---

# 5. Pull Requests

Every meaningful change should be introduced through a Pull Request.

A good Pull Request should explain:

* What changed
* Why it changed
* Screenshots (if UI changes)
* Related issue
* Testing performed

---

# 6. Commit Messages

Commit messages are part of a project's documentation. A well-written commit should clearly communicate **what changed** and provide enough context for future developers (or your future self) to understand the purpose of the change without reading the code.

Follow the Conventional Commits specification whenever possible.

Examples:

```text id="2mupbe"
feat(projects): add carousel section
fix(auth): resolve token refresh issue
docs(playbook): update deployment documentation
refactor(api): simplify report generation service
```

The optional **scope** (e.g., `projects`, `auth`, `api`, `playbook`) identifies the part of the codebase affected by the change. This makes it much easier to browse the Git history, understand where changes occurred, filter commits, and generate meaningful release notes.

For example:

```text id="t1z7gd"
feat: update carousel section
```

does not indicate **which** carousel or **which** part of the application was modified.

Whereas:

```text id="c5rh4y"
feat(projects): update carousel section
```

immediately tells readers that the change belongs to the **Projects** module.

Avoid vague commit messages such as:

```text id="htx7nw"
update
changes
fix
done
misc
```

These provide little historical context and make debugging, code reviews, and understanding project evolution significantly more difficult.

For a complete guide on writing effective commit messages—including commit types, scopes, multi-line commits, issue references, and best practices—see **[`guides/commit-messages.md`](guides/commit-messages.md)**.

---

# 7. Changelog

Maintain a `CHANGELOG.md`.

Record:

* Date
* Version
* Features
* Bug fixes
* Improvements
* Breaking changes

The changelog documents how the project evolves over time.

---

# 8. Engineering Journal

Maintain personal engineering notes while working.

Record:

* Interesting bugs
* Production discoveries
* Performance findings
* Commands used
* Useful references
* Lessons learned

These notes become valuable references for future documentation, incident reports, blog posts, portfolio case studies, and interview discussions.

The Engineering Journal is intended to be a personal working log. Significant production issues or outages should later be documented formally in the project's Production Incident Log.

---

# 9. Production Incident Log

Every significant production issue should be documented.

Suggested template:

```text
Date

Severity

Symptoms

Investigation

Root Cause

Resolution

Commands Used

Metrics Before

Metrics After

Lessons Learned
```

Writing the incident while it is fresh produces much higher-quality documentation later.

---

# 10. Architecture Decision Records (ADRs)

Record important technical decisions.

Example:

```
adr/
    001-use-postgresql.md
    002-introduce-prisma.md
    003-adopt-pm2.md
```

Each ADR should explain:

* Decision
* Context
* Alternatives considered
* Consequences

Future developers should understand *why* a decision was made.

Avoid modifying or deleting accepted ADRs. If a decision changes over time, create a new ADR that supersedes the previous one, preserving the architectural history of the project.

---

# 11. Runbooks

Create operational guides for common production tasks.

Examples:

```
runbooks/
    deployment.md
    rollback.md
    database-backup.md
    disk-space.md
```

Runbooks focus on **how to perform an operation**, not why.

---

# 12. Project Boards

Use project boards to visualize and manage work throughout the project's lifecycle.

Typical workflow:

```
Backlog
↓

Todo
↓

In Progress
↓

Review
↓

Done
```

This makes project status easier to understand.

---

# 13. Releases & Milestones

Group related work into releases.

Examples:

* v1.0
* v1.1
* Reporting Module
* Infrastructure Improvements

Milestones help communicate progress over time.

---

# 14. Architecture Diagrams

Maintain updated diagrams for:

* High-level architecture
* Request flow
* Deployment architecture
* Database relationships
* Service interactions

A diagram often communicates faster than pages of documentation.

---

# 15. Coding Standards

Maintain a dedicated coding standards document, for example:

```text
docs/
    coding-standards.md
```

The document should define conventions that contributors are expected to follow, such as:

* Naming conventions
* Folder structure
* Error handling
* Logging practices
* Testing conventions
* API design guidelines
* Formatting and style preferences

Documenting coding standards helps ensure consistency across the codebase, reduces subjective code review comments, and makes it easier for new contributors to understand the project's expectations.

---

# 16. Project Retrospectives

After major projects or releases, document:

* What went well
* What went poorly
* Lessons learned
* Improvements for future projects

Retrospectives encourage continuous improvement.

---

# 17. General Engineering Principles

Keep these principles in mind throughout every project:

* Build simple solutions before introducing complexity.
* Measure before optimizing.
* Automate repetitive work.
* Document important decisions.
* Keep infrastructure healthy.
* Monitor production continuously.
* Learn from every incident.
* Optimize for maintainability.
* Leave the codebase better than you found it.
* Treat documentation as part of the product.

---

# Engineering Checklist

Before considering a project complete, ask yourself:

## Repository

* [ ] `README.md` is complete and up to date.
* [ ] `LICENSE` is included (if applicable).
* [ ] `CONTRIBUTING.md` provides clear contribution guidelines.
* [ ] `CHANGELOG.md` has been updated.
* [ ] Pull Request and Issue templates are available (if applicable).

## Documentation

* [ ] Architecture is documented.
* [ ] Architecture diagrams are current.
* [ ] Engineering guides are available where needed.
* [ ] Coding standards are documented.
* [ ] Important architectural decisions are recorded (ADRs).
* [ ] Operational runbooks are available for common procedures.

## Development Workflow

* [ ] Issues have been tracked throughout development.
* [ ] Pull Requests were used for code reviews.
* [ ] Commit messages follow the project's convention.
* [ ] Releases or milestones have been created (if applicable).
* [ ] Release notes have been prepared.

## Operations & Maintenance

* [ ] Production incidents have been documented.
* [ ] Lessons learned have been recorded.
* [ ] A project retrospective has been completed.

---

# Final Thought

Code explains **how** a system works.

Documentation explains **why** it works that way.

An engineer's responsibility extends beyond writing software—it includes preserving the knowledge that allows others, including your future self, to understand, operate, maintain, and improve the system with confidence.

Well-maintained documentation turns individual knowledge into shared engineering knowledge, making projects easier to scale, maintain, and evolve over time.
