# Boquilhas — Overview

Boquilhas is the operational register for BQ repair processes and their real movement events.

The existing register/movement implementation is a valid base to preserve and evolve. The product model distinguishes the durable repair process from the production BQ context.

## Current model

The module records real movement events, including:

- Saída;
- Entrada;
- EntradaSemReparação.

Each repair process has a canonical `bq_repair_trace_id`.

The relationship is conceptually:

```text
BQ Tool
  ↓
bq_repair_trace_id
  ↓
movements
```

Before production association, the trace may be anchored to the canonical BQ `tool_id`.

When the relevant production context becomes known, the same trace is explicitly associated with `bq_id`:

```text
bq_repair_trace_id
  ↓
bq_id
```

The association does not replace the trace, reset it, or move its movements onto `bq_id`.

`bq_id` identifies the BQ Tool-in-production context. `bq_repair_trace_id` identifies the repair process. `movement_id` identifies an event inside that process.

The existing `boquilhas_id`-based persistence remains a valid implementation base and must be reconciled without discarding valid operational history.

## Planned evolution

The movement model must support:

- quantity in house;
- quantity out for repair;
- discrepancy when observed quantities do not reconcile with explainable movement history;
- discrepancy preserved as historical fact;
- unmatched quantity not inflating the accounted maximum/lot quantity.

Saldo/discrepancy is evaluated inside the repair trace. Broader BQ history is composed from its repair traces rather than by making `bq_id` carry one undifferentiated lifetime movement list.

Detailed rules:

- `REGISTO.md`
- `MOVIMENTOS.md`
- `HISTORICO.md`
- `DEFINICOES.md`
