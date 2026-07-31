# Contributing to Lydium Repositories

This document describes how work moves through our repositories — for both human engineers and AI coding agents (e.g. Claude Code) working under their direction. Most of our engineers are not experienced software developers, so this is written to be explicit rather than assume prior git/GitHub fluency.

## 1. Start with an Issue, not a prompt

Before asking an agent to write code, open an issue using one of the templates:

- **Agent-Ready Task** — for new work: a feature, a script, a change to existing behavior.
- **Bug Report** — for something that's broken.

Fill these out completely, especially:
- **Acceptance Criteria / Steps to Reproduce** — how you or the agent will know the work is actually done.
- **Out of Scope / Do Not Touch** — this is a safety field, not an optional one. Agents don't have institutional judgment about what's fragile; if you don't say "don't touch the CI config," it may decide to "helpfully" touch it.
- **Relevant Files & Paths** — saves the agent (and you) significant time versus letting it search the whole repo blind.

An agent working from a vague issue will either stall waiting for clarity it can't get, or fill the gap with a guess. A well-filled issue is the single biggest lever you have over the quality of what comes back.

## 1.5. Feature Requests vs. Agent-Ready Tasks

**Feature Request** is intentionally a low-bar template — anyone, including non-technical users of a tool, can open one without needing to know acceptance criteria, file paths, or scope boundaries. It is deliberately *not* labeled `agent-ready`, because it isn't yet scoped enough for an agent (or, often, even a human) to start work from directly.

The workflow is:

1. Anyone opens a **Feature Request** describing a problem or desired outcome in plain terms.
2. A maintainer (currently: Alex) reviews it during triage. This can be done with AI assistance — e.g. asking Claude to draft an Agent-Ready Task from the feature request's content — but a human should always review the result before it's posted, since only a human can judge whether the request is actually a good idea, correctly scoped, and safe to hand to an agent.
3. The maintainer opens a new **Agent-Ready Task** issue with the refined Summary, Acceptance Criteria, Relevant Files, and Out of Scope fields filled in, linking back to the original Feature Request (e.g. "Refines #123").
4. The original Feature Request is closed with a comment pointing to the new task, or left open and re-labeled if it needs more discussion first.

This separation exists so the burden of precise, agent-usable specification falls on whoever is doing triage — not on whoever first noticed the problem.

## 2. Point the agent at the issue

When you hand a task to Claude Code (or another agent), link the issue number and let it read the issue body directly rather than re-typing a summary from memory — the issue is the source of truth, and re-paraphrasing it introduces drift.

## 3. Never commit directly to `main`

All repositories enforce this via branch protection (see below), but the habit matters regardless of enforcement: always work on a branch and open a pull request, even for small changes.

## 4. Open a Pull Request

Every PR uses the standard template, which asks for:
- **What Changed** and **Acceptance Criteria Check** — confirm the PR actually satisfies what the issue asked for.
- **Testing Performed** — the exact commands run and their output. "Tests pass" alone isn't enough.
- **Scope Confirmation** — an explicit re-check that nothing outside the issue's stated scope was touched, and that no new dependencies snuck in unflagged.
- **Whether the PR was agent-authored** — this isn't a mark against the PR. It tells the reviewer how carefully to read the diff. Agent-authored code that hasn't yet been reviewed by a human should be flagged as such.

## 5. Review before merge

Every repository requires at least one approving review before merge. A few things to know:

- **PR authors cannot approve their own pull requests** — this is a hard GitHub rule, not a setting, and it can't be turned off.
- If you're the only person working in a given repository, you won't be able to satisfy that requirement through a second approval. In that case, the repo's ruleset includes a bypass allowing repository admins to merge without a second approval — but this doesn't replace real review. Read the diff yourself, check the Scope Confirmation claims against it, and confirm tests actually ran, before merging.
- **As soon as a second person is working in a repository, use them as a real reviewer** instead of relying on the bypass. That second pair of eyes is the actual point of this whole process — it's the main thing standing between an agent's plausible-looking mistake and production code.

## 6. CODEOWNERS

Larger or higher-risk repositories should have a `.github/CODEOWNERS` file assigning specific people as required reviewers for specific paths (e.g. a subsystem, or anything under `/infra`). Unlike issue and PR templates, CODEOWNERS is **not** inherited from this `.github` repository — it must be added to each individual repo that needs it. Ask in the team channel if you're setting up a new repo and aren't sure whether it needs one.

## 7. Secrets and credentials

Never put API keys, passwords, tokens, or connection strings into code, commit messages, issue descriptions, or PR descriptions — even in a private repository. Use environment variables or the repository's configured secrets manager, and reference them by name only. If you find a secret has been committed by mistake, treat it as compromised (rotate it) rather than just deleting the file — git history retains it.

If you discover an actual security vulnerability (not just an exposed secret you can rotate yourself), see [SECURITY.md](./SECURITY.md) — do not open a public issue for it.

## 8. Merging

Repositories default to **Squash and merge**. This collapses an agent's often-noisy commit history (intermediate fixes, typo corrections) into a single clean commit on `main`. Write a clear final commit message summarizing the change — GitHub pre-fills this from the PR title and description, but it's worth checking it makes sense as a standalone line in the project history.
