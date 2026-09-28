# Open Decisions

This file contains unresolved authority questions only.

An item here is not canonical until the owner decides it and the resulting durable rule is promoted into the appropriate authority file.

## OD-001 — Resumo persistence identity

**Question:** Does Resumo remain a read projection over an existing `jobon_id` production context, or does DMO require a separate persisted `resumo_id` identity?

**Current safe authority:**
- Resumo is a Controlo function/tab that summarizes one production context.
- It may compose existing function records at read time.
- Peso, Comparação, Pegamentos, and Folha must not acquire a `resumo_id` parent merely because Resumo displays them.

**Not decided:**
- Whether Resumo itself needs a durable persisted record.
- Whether `resumo_id` should exist at all.
- Which Resumo-specific facts, if any, would justify independent persistence.

**Owner decision required:** Confirm one of the two models and state the real operational event/facts that justify persistence if `resumo_id` is required.

## OD-002 — Controlo production-level identity

**Question:** Is `controlo_id` a canonical durable identity in DMO?

**Current safe authority:**
- Controlo contains several distinct functions/workflows.
- UI containment does not imply database parent-child ownership.
- Existing natural anchors remain authoritative where they already express the domain, such as Peso through its real production/component context.
- No generic Controlo parent may be inferred merely for navigation convenience.

**Not decided:**
- Whether a standalone `controlo_id` must exist.
- The exact event that creates it.
- Whether it is one-to-one with `jobon_id`, optional, or has another lifecycle.
- Which facts, if any, belong to it rather than to Job On or the function-specific records.

**Owner decision required:** Confirm whether `controlo_id` exists and, if it does, define its minimum durable purpose, creation moment, relation to `jobon_id`, and explicit non-ownership boundaries.
