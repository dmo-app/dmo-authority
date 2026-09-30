# Authority Guard

This repository is intended to be changed through reviewed pull requests, not silent direct changes to `main`.

The protection model is:

```text
change proposed
→ pull request
→ automated process check
→ stand by
→ authorized reviewer / CODEOWNER reviews the meaning
→ approve or reject
→ merge only after approval
```

## What automation may enforce

The automated guard may enforce objective process requirements, such as:

- the change is going through a pull request;
- commits carry the required self-contained rationale;
- required status checks complete successfully.

It may also surface **non-blocking review notices** when a change touches drift-sensitive areas such as identities, legacy role wording, persistence relations, lifecycle decisions, Tool/lot semantics, or document boundaries.

These notices exist to focus the reviewer. They are not verdicts and never fail the workflow by themselves.

It must not decide whether a product/domain change is semantically correct.

A legitimate new owner decision may intentionally change existing authority. Automation must not force that change back into an older rule simply because the older rule was previously canonical.

## Human review owns semantic approval

Changes to authority remain pending until an authorized reviewer or CODEOWNER explicitly approves them.

The reviewer uses the notices as a review aid, then decides whether the proposed change:

- contradicts current authority intentionally or accidentally;
- represents a valid new owner decision;
- needs clarification before merge;
- should be rejected.

The guard is therefore a process sentinel, not an autonomous architect or domain judge.

## Repository-side files

1. `.github/CODEOWNERS` identifies the owner of canonical authority.
2. `.github/workflows/authority-guard.yml` validates process requirements only.
3. `.github/pull_request_template.md` requires the proposed change, reason, support and boundaries to be visible before review.

## Required GitHub repository settings

To make the stand-by behavior enforceable, protect `main` with a GitHub ruleset / branch protection that:

- requires a pull request before merging;
- requires at least one approval;
- requires review from Code Owners;
- dismisses stale approvals when new commits are pushed;
- requires the `authority-guard` status check to pass;
- blocks force pushes;
- blocks branch deletion;
- prevents bypass for normal development/agent accounts.

With these settings, an agent may propose a change, but cannot silently place it into `main`. The change waits for human approval.

These repository settings are separate from the files in this repository and must be enabled in GitHub repository settings by an account with administration permission.
