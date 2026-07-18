# Commit Message Structure Enforcement

As projects grow, maintaining consistent commit messages becomes increasingly difficult. While developers can be encouraged to follow commit message conventions, relying solely on discipline often leads to inconsistent Git history.

To address this, many engineering teams automate commit message validation.

Rather than reviewing commit messages after they have already been pushed, automated validation prevents incorrectly formatted commits from entering the repository.

---

# Why Enforce Commit Messages?

Consistent commit messages improve:

* Code reviews
* Git history readability
* Release note generation
* Semantic versioning
* Team collaboration
* Repository maintainability

For example, these commits provide little value:

```text id="vv5bd5"
update
fix
changes
done
misc
```

Whereas these immediately communicate the purpose of each change:

```text id="y8hv5x"
feat(auth): add password reset

fix(database): prevent duplicate migrations

docs(playbook): add incident management guide

perf(api): optimize campaign report generation
```

Automated validation ensures that every commit follows the agreed convention.

---

# When Should You Enforce It?

Commit message enforcement is generally unnecessary for small personal projects.

It becomes increasingly valuable when:

* Multiple developers contribute.
* Pull Requests are used.
* Releases are automated.
* Changelogs are generated automatically.
* Semantic Versioning is adopted.
* Maintaining a clean Git history is important.

---

# Common Enforcement Methods

There are several ways to validate commit messages.

## Local Git Hooks

Git can execute scripts before a commit is accepted.

A `commit-msg` hook validates the commit message immediately after it is written.

Advantages:

* Immediate feedback
* Fast validation
* Prevents incorrect commits before they exist

Limitations:

* Every contributor must install the hook.
* Hooks are not shared automatically with the repository.

---

## Server-side Hooks

Repositories hosted on self-managed Git servers can validate commits before they are accepted.

Examples include:

* `pre-receive`
* `update`

Advantages:

* Rules apply to every contributor.
* Cannot be bypassed locally.

Limitations:

* Not available on all Git hosting platforms.

---

## CI/CD Validation

Continuous Integration pipelines can reject Pull Requests containing invalid commit messages.

Advantages:

* No local setup required.
* Centralized enforcement.
* Easy to maintain.

Limitations:

* Developers receive feedback after pushing their commits.

---

## Commitlint

Many JavaScript and TypeScript projects use **Commitlint** together with **Husky** to validate Conventional Commit messages automatically.

This is one of the most widely adopted solutions for modern development teams.

---

# Example Validation

The validator should reject commits such as:

```text id="09e8we"
update
fix
done
misc
new changes
```

And accept commits like:

```text id="4vw4al"
feat(auth): add password reset

fix(api): prevent duplicate requests

docs(playbook): update deployment workflow

perf(database): optimize report query
```

---

# Example Implementation

There are several ways to enforce commit message standards. The appropriate approach depends on the project's technology stack, team size, and development workflow.

## Option 1 — Commitlint + Husky (Recommended for Node.js Projects)

For JavaScript and TypeScript projects, **Commitlint** together with **Husky** is the most common approach for enforcing Conventional Commit messages.

### 1. Install the required packages

```bash
npm install --save-dev @commitlint/cli @commitlint/config-conventional husky
```

### 2. Configure Commitlint

Create a `commitlint.config.js` file in the project root:

```javascript
module.exports = {
  extends: ["@commitlint/config-conventional"],
};
```

### 3. Initialize Husky

```bash
npx husky init
```

### 4. Create the commit message hook

Inside `.husky/commit-msg`:

```bash
npx --no -- commitlint --edit "$1"
```

Now, every commit is automatically validated before it is accepted.

For example:

```text
feat(auth): add password reset
```

will be accepted, while:

```text
update
```

will be rejected.

### Advantages

* Industry-standard solution
* Easy to maintain
* Configuration is version controlled
* Shared automatically with every contributor
* Supports custom commit rules

---

## Option 2 — Native Git Hooks

Git provides a built-in `commit-msg` hook that can validate commit messages before a commit is created.

This approach works with any programming language and requires no external dependencies.

However, Git hooks are stored locally inside:

```text
.git/hooks/
```

These files are **not tracked by Git**, meaning every contributor must install them manually unless additional tooling is used.

Native Git hooks are well suited for personal projects or small teams but become difficult to manage across larger teams.

---

## Option 3 — GitHub Actions (CI Validation)

Another approach is validating commit messages during Continuous Integration.

When a Pull Request is opened, GitHub Actions can verify that every commit follows the project's conventions.

Advantages include:

* No local setup required
* Rules apply consistently across all contributors
* Easy to integrate into existing CI/CD pipelines

The downside is that developers receive feedback **after** pushing their commits rather than immediately during development.

---

## Choosing an Approach

| Approach              | Best For                      | Shared with Team | Immediate Validation |
| --------------------- | ----------------------------- | :--------------: | :------------------: |
| Native Git Hooks      | Personal projects             |         ❌        |           ✅          |
| Commitlint + Husky    | Node.js / TypeScript projects |         ✅        |           ✅          |
| GitHub Actions        | Any GitHub project            |         ✅        |           ❌          |
| Server-side Git Hooks | Self-hosted Git servers       |         ✅        |           ✅          |

---

## Recommendation

For most modern GitHub projects:

* Use **Commitlint + Husky** to provide immediate feedback during development.
* Optionally validate commit messages again in **GitHub Actions** to ensure all commits entering the repository follow the agreed convention.

This combination provides a consistent developer experience while maintaining a clean, searchable, and reliable Git history.

---

# Best Practices

* Document the expected commit message format.
* Teach developers the convention before enforcing it.
* Automate validation only after the team agrees on the standard.
* Keep validation rules simple and easy to understand.
* Prefer informative error messages when rejecting commits.
* Review the enforcement rules periodically as the project evolves.

---

# Relationship to Commit Messages

This guide complements **[`docs/guides/commit-messages.md`](../guides/commit-messages.md)**.

The **Commit Messages** guide explains **how to write meaningful commit messages**.

This guide explains **how engineering teams automatically enforce those conventions** to maintain a clean, consistent Git history.

Together, these practices improve collaboration, simplify project maintenance, and ensure that Git history remains a valuable source of engineering knowledge rather than a collection of inconsistent commit messages.
