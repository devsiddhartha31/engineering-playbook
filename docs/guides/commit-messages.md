# Commit Messages

Git is more than a version control system—it is the historical record of a software project. Every commit represents an engineering decision, and its message explains **what changed** and, when necessary, **why the change was made**.

A project's Git history should be understandable months or even years after the code was written. Well-written commit messages make debugging, code reviews, releases, and onboarding significantly easier.

---

# Why Commit Messages Matter

Imagine opening a repository six months after your last contribution.

Which history would you rather read?

**Example 1**

```text
update
fix
changes
done
misc
```

These messages provide almost no useful information.

Now compare that with:

```text
feat(campaigns): add audience scheduling
fix(auth): resolve token refresh issue
perf(database): optimize report query using composite index
docs(playbook): add nginx disk exhaustion case study
refactor(api): extract campaign validation service
```

Without opening a single file, you already understand:

* What changed
* Which part of the application changed
* The purpose of each change
* The evolution of the project

A good commit history becomes searchable engineering documentation.

---

# Conventional Commits

This playbook follows the **Conventional Commits** specification.

General format:

```text
<type>(<optional-scope>): <description>
```

Example:

```text
feat(projects): add carousel section
```

This consists of three parts:

* **Type** – the category of change.
* **Scope** – the module or component affected.
* **Description** – a concise summary of the change.

---

# Understanding Commit Types

Choose the commit type based on the primary purpose of the change.

## feat

Introduces a new feature.

```text
feat(auth): add Google OAuth login

feat(campaigns): support scheduled publishing
```

---

## fix

Resolves a bug.

```text
fix(api): prevent duplicate event processing

fix(login): validate expired refresh tokens
```

---

## docs

Documentation-only changes.

```text
docs(playbook): add deployment workflow

docs(readme): update installation guide
```

---

## refactor

Improves code structure without changing behavior.

```text
refactor(api): extract validation middleware

refactor(database): simplify query builder
```

---

## perf

Performance improvements.

```text
perf(database): optimize report generation query

perf(cache): reduce Redis lookups
```

---

## test

Adds or updates tests.

```text
test(api): add unit tests for campaign service
```

---

## build

Changes related to dependencies or build tools.

```text
build: upgrade Prisma to v6

build: update Docker base image
```

---

## ops

Infrastructure or operational changes.

```text
ops(deployment): configure PM2 startup

ops(ci): add production deployment workflow
```

---

## style

Formatting changes only.

```text
style: remove trailing whitespace
```

---

## chore

Maintenance work that does not fit other categories.

```text
chore: initial commit

chore: update .gitignore

chore: rename environment variables
```

---

# Why Scopes Matter

One of the most overlooked parts of Conventional Commits is the **scope**.

Compare these two commits:

```text
feat: update carousel section
```

versus

```text
feat(projects): update carousel section
```

The first tells you **what** happened.

The second tells you:

* what happened
* where it happened

This becomes extremely valuable as projects grow.

Example:

```text
feat(auth): add password reset
feat(api): support bulk upload
feat(database): add audit log table
feat(ui): redesign dashboard cards
feat(reports): export analytics to CSV
```

A developer browsing the Git history can immediately locate changes related to a specific module.

## Choosing Scopes

Scopes should represent logical parts of the project.

Examples:

```text
auth
users
projects
campaigns
dashboard
database
api
ui
reports
payments
notifications
playbook
deployment
```

Avoid using issue numbers or unrelated identifiers as scopes.

Good:

```text
fix(auth): resolve session timeout
```

Bad:

```text
fix(issue-42): resolve session timeout
```

---

# Writing Good Descriptions

Descriptions should be:

* concise
* specific
* written in the imperative mood
* written in the present tense

Good:

```text
feat(auth): add Google login

fix(api): prevent duplicate requests

docs(playbook): explain pull request workflow
```

Bad:

```text
Added login

Fix bug

Updated documentation

Misc changes
```

Think of every commit message as completing this sentence:

> This commit will...

Examples:

* add campaign filtering
* optimize report generation
* remove deprecated endpoint

---

# Multi-line Commit Messages

Some commits need more than a one-line description.

Example:

```text
perf(database): optimize report generation query

Replace multiple nested queries with a single aggregated query.
Add a composite index on timestamp and campaign_id.

Average report generation time decreased from 18 seconds
to approximately 2 seconds during testing.

Refs #84
```

The body explains **why** the change exists rather than repeating the title.

Use the body when:

* introducing major features
* making architectural changes
* explaining complex fixes
* documenting trade-offs

---

# Referencing Issues

If the project uses GitHub Issues, reference them in commit messages.

Examples:

```text
fix(auth): resolve refresh token bug

Closes #42
```

```text
feat(campaigns): support recurring schedules

Refs #103
```

Common keywords:

```text
Closes #12
Fixes #18
Resolves #24
Refs #31
```

---

# Breaking Changes

If a commit introduces a breaking API or behavior change, indicate it clearly.

Example:

```text
feat(api)!: remove legacy authentication endpoint
```

Include additional explanation:

```text
BREAKING CHANGE:
The legacy authentication endpoint has been removed.
Applications should migrate to /api/v2/auth.
```

Breaking changes should never surprise downstream users.

---

# Atomic Commits

One commit should represent **one logical change**.

Bad example:

```text
feat: update dashboard
```

Actually changed:

* redesigned dashboard
* fixed login
* updated README
* added analytics
* renamed folders

Good example:

```text
feat(dashboard): redesign analytics cards

fix(auth): resolve login redirect

docs(readme): update setup instructions

refactor(api): rename campaign service
```

Smaller commits are easier to:

* review
* revert
* debug
* understand

---

# Good vs Bad Examples

## Good

```text
feat(projects): add project carousel

fix(database): prevent duplicate migrations

docs(playbook): add engineering handbook

perf(api): reduce response time for campaign reports

ops(deployment): configure PM2 startup
```

## Bad

```text
update

fix

new code

changes

done

misc
```

---

# Real-World Examples

Examples based on this playbook:

```text
docs(playbook): complete production CMS engineering playbook

docs(playbook): add report query optimization case study

perf(database): create concurrent index for EventLog timestamp

ops(server): configure logrotate for nginx logs

fix(api): release idle PostgreSQL connections

refactor(reports): simplify CSV export pipeline
```

---

# Common Mistakes

Avoid:

* Writing vague messages.
* Combining unrelated changes into one commit.
* Omitting scopes in large projects.
* Using the wrong commit type.
* Writing descriptions in the past tense.
* Using commit messages like "update" or "fix".

---

# Team Conventions

For consistency within a project:

* Use lowercase commit types.
* Keep descriptions concise.
* Prefer meaningful scopes.
* Keep one logical change per commit.
* Reference related issues when applicable.
* Write documentation commits just as carefully as code commits.

Consistency makes the project's history easier to navigate and maintain.

---

# Commit Message Checklist

Before committing, ask yourself:

* [ ] Is the commit focused on one logical change?
* [ ] Does the type accurately describe the change?
* [ ] Is the scope appropriate?
* [ ] Is the description concise and specific?
* [ ] Would another developer understand this commit without opening the code?
* [ ] If necessary, have I explained **why** in the commit body?
* [ ] Have I referenced the related issue or task?

---

# Automating This Process

Following commit message conventions consistently can be challenging, especially as projects and teams grow. Rather than relying solely on manual discipline, many engineering teams automate commit message validation to ensure every commit follows the agreed standard.

Techniques such as Commitlint, Husky, Git hooks, and GitHub Actions can automatically reject incorrectly formatted commit messages before they become part of the project's history.

For implementation approaches and recommended tooling, see [commit message enforcement](../advanced/commit-message-enforcement.md).

---

# Final Thoughts

Every line of code eventually changes, but the history explaining **why** those changes happened remains valuable throughout the lifetime of a project.

Treat commit messages as part of your engineering documentation, not as an afterthought. A clean, descriptive Git history improves collaboration, simplifies maintenance, and provides future developers—including your future self—with the context needed to understand how and why the software evolved.
