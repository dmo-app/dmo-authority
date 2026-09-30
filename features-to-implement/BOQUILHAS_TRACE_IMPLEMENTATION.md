# Boquilhas Repair Trace Implementation

**Status:** CANONICAL MODEL DEFINED — IMPLEMENTATION RECONCILIATION REQUIRED

**Type:** Boquilhas persistence / migration / workflow implementation

## Purpose

Bring the existing Boquilhas implementation into line with the current canonical repair-trace model without losing real operational history.

The existing `boquilhas_id`-based register/movement persistence is a valid implementation base. It must be adapted, not discarded merely to rename or normalize schema.

## Canonical identity model

```text
tool_id
= canonical physical BQ Tool

jobon_id
-> bq_id
-> one bq_repair_trace_id
-> many movement_id
```

A `bq_id` identifies that BQ Tool in one production context.

A `bq_repair_trace_id` identifies the single movement trace for that BQ production context.

The trace is **not** created per repair trip. Several cycles belong to the same trace:

```text
trace
-> saida
-> entrada
-> saida
-> entrada_sem_reparacao
-> ...
```

A new production/BQ context receives a new trace even when the same canonical `tool_id` is reused.

## Pre-production trace

A trace may begin before the relevant Job On exists.

```text
tool_id
-> bq_repair_trace_id
-> movements

bq_id = unresolved
```

When Job On later creates the matching `bq_id` that references the same canonical `tool_id`, the **same trace** is associated when the match is unambiguous.

Existing movements are preserved. The trace is not recreated and its history is not reset.

While `bq_id` is unresolved, the UI must expose the missing Job On association condition.

## Late returns

A later production never takes ownership of an earlier trace.

Movements that belong to the earlier production remain on that earlier trace even after a new production begins.

## Movement/discrepancy behavior

Implementation must also preserve the current Boquilhas movement rules in:

- `modules/boquilhas/MOVIMENTOS.md`;
- `modules/boquilhas/REGISTO.md`;
- `modules/boquilhas/HISTORICO.md`.

Do not collapse the repair trace into a flat lifetime movement list directly on `bq_id`.

## Open product decision that must remain open

The current blueprint intentionally leaves one cardinality question unresolved:

> May the same canonical BQ `tool_id` have more than one simultaneous pending `bq_repair_trace_id` with `bq_id = null`?

This question is separate from the physical-use invariant that one physical Tool cannot be in simultaneous use in two different machine/production contexts.

The implementation must not silently decide unresolved-trace cardinality from physical exclusivity, an existing/provisional database index, or implementation convenience.

Automatic association is canonical only when the intended pending match is unambiguous. If it is not unambiguous, implementation of that ambiguous case is blocked pending an explicit product decision.

## Implementation work

Planning/Architect must inspect the current Boquilhas schema and services and define a migration/reconciliation path that:

- preserves existing real register and movement history;
- introduces or reconciles canonical `bq_repair_trace_id`;
- makes movements belong to the trace;
- keeps one trace for all movement cycles of one BQ production context;
- supports a trace that starts from `tool_id` before Job On;
- later attaches that same trace to the matching `bq_id`;
- preserves late returns on the original trace;
- does not mutate historical production contexts.

## Reviewer checks

Reject an implementation that:

- creates a new trace for every `saida`;
- treats `bq_id` as the canonical physical BQ identity;
- changes the `tool_id` underneath an existing `bq_id`;
- recreates a pre-production trace when Job On appears;
- moves old movements to the current production;
- destroys valid existing operational history during migration;
- chooses a rule for multiple simultaneous pending traces without resolving the open product decision.
