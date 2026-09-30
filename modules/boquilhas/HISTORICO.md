# Boquilhas — Histórico

Boquilhas history is composed from durable repair traces and the real movement facts recorded inside each trace.

## Repair-trace history

`bq_repair_trace_id` identifies one repair process.

Its movements remain grouped under that trace before and after association to production.

A later association to `bq_id`, or a later machine/production change, must not move those historical movements onto another context.

`bq_id` identifies the BQ-in-production context; it is not the direct lifetime container for all Boquilhas movements.

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
