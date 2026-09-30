# Boquilhas — Overview

Boquilhas records the real repair movements of BQ Tools while keeping those movements grouped by production context.

The existing register/movement implementation is a valid base to preserve and evolve.

## Canonical relationship

```text
tool_id
= canonical physical BQ

jobon_id
→ bq_id
→ one bq_repair_trace_id
→ many movement_id
```

`bq_id` identifies that BQ in one specific production and references its canonical `tool_id`.

`bq_repair_trace_id` identifies the movement trace for that BQ production context. It is not created per repair trip. Several cycles may exist inside the same trace:

```text
trace
→ saida
→ entrada
→ saida
→ entrada_sem_reparacao
→ saida
→ ...
```

A new production creates a new `bq_id` and a new trace, even if both the previous and new `bq_id` reference the same physical BQ `tool_id`.

## Pre-production trace

A repair movement may need to be recorded before the relevant Job On exists.

In that case:

```text
tool_id
→ bq_repair_trace_id
→ movements

bq_id = null
```

The trace keeps that identity.

For one canonical BQ `tool_id`, there may be **at most one** pre-production trace with `bq_id = null` at a time.

This does not mean one trace per Tool. The same `tool_id` may accumulate many traces historically because each production has its own `bq_id` and trace. The restriction applies only while a trace is still waiting for its production association.

There is no trace `open` / `closed` lifecycle. The relevant distinction is only:

```text
pre-production trace
→ bq_id = null

associated production trace
→ bq_id = <production_bq_id>
```

When Job On later creates the corresponding `bq_id`, both the pending trace and the new `bq_id` reference the same canonical `tool_id`. Because only one unresolved trace can exist for that `tool_id`, the trace is associated automatically:

```text
pending trace.tool_id
==
new bq_id.tool_id

→ same bq_repair_trace_id
→ attach to bq_id
→ preserve existing movements
```

While `bq_id` remains unresolved, the UI exposes a persistent Job On association warning derived from the missing association.

If no pending trace exists when the production `bq_id` is created, that production uses a new trace. If a trace starts after the production `bq_id` already exists, it is associated immediately to that production context.

## Late returns

A later production never takes ownership of an earlier trace.

```text
Production A
→ bq_id A
→ trace A
→ saida 5

Production B
→ bq_id B
→ trace B

late entrada 5
→ trace A
```

The current machine/production context is not used to reassign old movements.

## Movement facts

Movement types remain:

- Saída;
- Entrada;
- EntradaSemReparação.

The existing `boquilhas_id`-based persistence remains a valid implementation base and must be reconciled without discarding real operational history.

Quantity-in-house, quantity-out and discrepancy are projections/derivations over movement facts. Their detailed mathematics may be refined independently of this identity structure.

Detailed rules:

- `REGISTO.md`
- `MOVIMENTOS.md`
- `HISTORICO.md`
- `DEFINICOES.md`
