# Boquilhas — Histórico

Boquilhas history is composed from durable repair traces and the real movement facts recorded inside each trace.

## Repair-trace history

`bq_repair_trace_id` identifies the movement trace of one BQ production context (`bq_id`). It may contain several repair cycles.

Its movements remain grouped under that trace before and after association to production.

A pre-production trace keeps the same identity when later associated to its `bq_id`. A later machine/production change must not move those historical movements onto the new production trace.

`bq_id` identifies the BQ-in-production context and has one production trace. The canonical `tool_id` may repeat across productions, while each production receives a different `bq_id` and trace. Therefore one physical Tool may have many historical traces over time. The only cardinality restriction is that the same `tool_id` cannot have two simultaneous pre-production traces whose `bq_id` is still unresolved.

## Movement history

A recorded movement remains part of the historical record of its `bq_repair_trace_id`.

The movement system preserves what was observed rather than silently rewriting the past to make quantities mathematically tidy.

## Discrepancy history

A discrepancy produced by a movement is a historical fact of that repair trace.

It is not a temporary debt to be cancelled by a later movement.

Later movements must not silently compensate or erase an earlier discrepancy merely because their numbers happen to offset one another.

The accumulated discrepancy for a repair trace is derived from the movement discrepancies that occurred inside that trace.

Broader BQ history may compose several traces, but one trace's discrepancy is never silently transferred into another.

## Editing

Where explicit movement editing is allowed, it must remain auditable.

Editing must not be used as an automatic reconciliation mechanism.

The exact permitted edit boundary follows the movement rules in `MOVIMENTOS.md` and the implemented audit behavior.
