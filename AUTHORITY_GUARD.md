# Authority Guard

This repository is intended to be changed through reviewed pull requests, not silent direct changes to `main`.

Repository-side files provide three protections:

1. `.github/CODEOWNERS` requires the repository owner to review canonical authority when branch rules enforce code-owner review.
2. `.github/workflows/authority-guard.yml` validates the required commit rationale and protects a small set of already-settled invariants from accidental drift.
3. `.github/pull_request_template.md` makes authority changes and their boundaries explicit before merge.

The automated guard is deliberately conservative. It is a drift detector, not an autonomous architect. It must not invent new canon or decide unresolved product questions.

## Required GitHub repository settings

For the guard to be enforceable rather than advisory, protect `main` with a GitHub ruleset / branch protection that:

- requires a pull request before merging;
- requires at least one approval;
- requires review from Code Owners;
- dismisses stale approvals when new commits are pushed;
- requires the `authority-guard` status check to pass;
- blocks force pushes;
- blocks branch deletion;
- prevents bypass for normal development/agent accounts.

Signed commits may also be required if desired.

These repository settings are separate from the files in this repository and must be enabled in GitHub repository settings by an account with administration permission.
