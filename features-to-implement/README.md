# Implementation Slices

This directory is a **fresh-start implementation backlog** derived from the current DMO blueprint.

Each file represents one bounded slice of the product that must be built so the new application reaches the canonical behavior described in the owning module documents.

The files in this directory must be written as:

```text
what the new application must support
→ what the slice receives
→ what it creates
→ what it reads
→ what it persists
→ what it derives
→ which owning contracts it depends on
→ what it must not invent
→ how completion is proven
```

They must **not** be written as repair notes for an older application.

## Source-of-truth boundary

```text
modules/ + cross-cutting blueprint
= product truth

this directory
= implementation slices derived from that truth

IMPLEMENTATION_STATUS.md
= implementation-state evidence only
```

A slice never overrides its owning canonical module document.

If a slice and the canonical blueprint disagree, the canonical blueprint wins and the slice must be corrected.

## Fresh-start rule

Do not frame a slice as:

- "the old app already has...";
- "the backup is missing...";
- "recover/migrate the previous schema...";
- "align the current endpoint/service...";
- "preserve migration X...";
- "complete the existing implementation...".

Those may be useful historical facts elsewhere, but they are not the contract for constructing the new application.

Frame the slice instead as:

```text
What must exist in the application we want to build?
```

The Developer must be able to implement the slice without needing to know that an older DMO application existed.

## Required slice content

Before a slice is given to a Developer, it should answer as much as the current blueprint safely allows:

- objective / user-visible result;
- owning domain/capability/workflow;
- operation anchor(s);
- canonical IDs received;
- canonical IDs created;
- user-supplied facts;
- backend-resolved facts/context;
- reads and their owning contracts;
- persisted facts and ownership;
- derived/read-model values that must not become duplicate truth;
- concurrency requirements where the operation mutates durable state;
- expected refusals/error cases;
- frontend states relevant to the operation;
- dependencies on other domains/capabilities;
- explicit out-of-scope behavior;
- regression boundaries;
- acceptance evidence / reviewer checks.

Do not add fields merely to make this template look complete. Only document facts that are actually known.

## Readiness gate

Every slice is either:

```text
READY
→ all functional decisions required by this slice are available
→ backend/frontend contracts can be completed
→ implementation may proceed

BLOCKED
→ a required functional decision is missing
→ record the exact missing question
→ update the owning canonical blueprint when decided
→ then return to the slice
```

A Developer must never resolve a BLOCKED product question by architectural preference or implementation convenience.

A larger feature may be split so that decided sub-slices can proceed while only the ambiguous sub-slice remains blocked.

## Cross-domain dependency rule

When a slice needs information owned elsewhere:

```text
consumer needs X
→ identify owner of X
→ use the owner's explicit contract/relation

if the existing contract is insufficient
→ expand the owning contract deliberately
→ verify the owner regression boundary
→ then consume it
```

Do not copy another domain's truth into the consumer merely for convenience.

Do not read foreign implementation internals simply because they are technically reachable.

## Identity rule

Search attributes, labels and filters never become derived identities.

```text
filters reduce candidates
!=
filters determine identity
```

Clients may send canonical IDs already issued by an owning backend workflow, but they never mint, derive or guess new canonical IDs.

## File lifecycle

1. A functional rule is decided in its owning canonical blueprint document.
2. A bounded implementation slice is written from that rule.
3. Backend/frontend contract questions are closed for that slice.
4. Planning/Architect prepares a small but complete Developer package.
5. Developer implements only that slice.
6. Review verifies the implementation against the slice and canonical blueprint.
7. Once completed, the durable product rule remains only in the canonical blueprint. The slice may be marked complete or retired; it must not become a second permanent copy of product behavior.

## Current migration of this directory

Some existing files in this directory were originally written as recovery/alignment notes against older application baselines.

They are being rewritten **one by one** into fresh-start slices.

Until a file has been rewritten, treat recovery/baseline wording inside it as historical drafting material, not as the desired framing for the new implementation.

Do not use an unreconciled file as a Developer contract without first converting it to the fresh-start structure above.
