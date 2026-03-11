---
name: commit-message-writer
description: Draft conventional commit messages that explain why a change exists instead of listing what changed. Use when trying to write a git commit message.
---

# Commit Message Writer

## Overview

Draft commit messages from intent. Prefer the reason, risk reduction, or user outcome behind the change over a summary of edited files.

## Workflow

1. Gather rationale before drafting.
2. Choose the narrowest conventional commit type that matches the change.
3. Write a why-first subject line under 50 characters.
4. Add body details only when they clarify context, tradeoffs, or follow-up implications.
5. Wrap every body line at 72 characters.

## Build Context

Read context in this order until the rationale is clear:

1. User request and follow-up discussion with the agent.
2. ADRs, issue text, design notes, or nearby project docs.
3. The explored implementation: comments, naming, tests, error paths, and behavior changes.
4. Recent git history when it explains the broader thread or ongoing cleanup.

Prefer the highest-signal reason available. Do not echo the diff when a better product, operational, or maintenance motivation is available.

When the rationale is incomplete, infer it from the surrounding context and choose the most defensible explanation. Prefer intent such as preventing regressions, restoring behavior, reducing operator burden, or making future work possible.

## Write the Subject

Use conventional commits:

- `feat`: introduce user-facing capability
- `fix`: correct broken or risky behavior
- `refactor`: improve structure without intended behavior change
- `perf`: improve performance characteristics
- `test`: add or fix tests
- `docs`: change documentation only
- `build`, `ci`, `chore`: repository or tooling maintenance

Keep the subject in the form:

`type(scope): why`

Rules:

- Stay at 50 characters or fewer.
- Focus on intent or effect, not implementation steps.
- Use a scope only when it adds meaning.
- Prefer active phrasing that explains the reason for the change.
- Avoid filler such as `update`, `stuff`, `misc`, or raw file names unless they are the actual context.

Good patterns:

- `fix(auth): block expired token reuse`
- `refactor(api): simplify pagination flow`
- `feat(billing): support invoice retry recovery`

Avoid why-less summaries:

- `fix(auth): change token validation`
- `refactor(api): move pagination helper`

## Write the Body

Only add a body when it helps the reader understand context that does not fit in the subject.

Format:

1. First line: subject
2. Second line: blank
3. Third line onward: wrapped body lines at 72 characters

Use the body for:

- background that motivated the change
- constraints or tradeoffs
- notable behavior changes
- follow-up considerations

Do not use the body to restate the exact file edits unless that detail is needed to explain the rationale.

## Output Checklist

Before returning the message, verify:

1. The subject uses a valid conventional commit type.
2. The subject is 50 characters or fewer.
3. The subject explains why, not just what changed.
4. The second line is blank when a body is present.
5. Every body line is 72 characters or fewer.
6. The final message is ready to paste into `git commit`.
