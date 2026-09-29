# Boquilhas — Overview

Boquilhas is the operational register for BQ repair movements.

The existing register and movement implementation is the valid base to preserve and evolve.

## Current model

The module records real movement events, including:

- Saída;
- Entrada;
- EntradaSemReparação.

The existing `boquilhas_id`-based register must not be treated as an error merely because another identity model appeared in later documentation.

A separate `bq_repair_trace_id` is not currently required.

## Planned evolution

The main evolution is the saldo/discrepancy behavior:

- quantity in house;
- quantity out for repair;
- discrepancy when observed quantities do not reconcile with explainable movement history;
- discrepancy preserved as historical fact;
- unmatched quantity must not inflate the accounted maximum/lot quantity.

This evolution should be implemented by adapting the existing movement system wherever possible.

Detailed rules:

- `REGISTO.md`
- `MOVIMENTOS.md`
- `HISTORICO.md`
- `DEFINICOES.md`
