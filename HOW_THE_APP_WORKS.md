# DMO Beta — How the App Works

This document is the fast canonical map of the application.

It explains what DMO is, the principal relationships between modules, and the general operational flow. Detailed rules live in the module documents under `modules/`.

The intended reading pattern is:

```text
HOW_THE_APP_WORKS.md
→ understand the application and its main relations

modules/<module>/
→ understand the detailed behavior of the area being worked on
```

Do not use this file as a substitute for the detailed module documents.

---

## 1. What DMO is

DMO is an operational factory application for glass-container manufacturing.

It connects:

- Tools and their stable identity;
- production occurrences;
- the exact Tools used in each production;
- quality-control measurements and human decisions;
- Boquilhas repair movements;
- users, access templates and module permissions;
- historical facts that must remain attributable to the context in which they occurred.

The broad principle is:

> Persist the real operational facts and durable relations. Compose the context required by each screen when it is read.

The application must not create new identities, ownership relations or duplicated facts merely to make one UI or query more convenient.

---

## 2. Main identity and context chain

The central production relationship is:

```text
tool_id
→ canonical Tool

jobon_id
→ one production occurrence

cm_id / mf_id / bq_id
→ the selected Tool in that production context
```

A Tool is registered in Ferramentas and may be used in many productions.

A Job On establishes one production occurrence and selects the relevant Tools.

The component contexts connect the selected canonical Tool to that specific Job On and preserve the required production-time context.

A different lot of the same reference is a different canonical Tool and receives a different `tool_id`.

Detailed identity rules live in `IDENTITIES.md` and the relevant module documents.

---

## 3. General application flow

At a high level:

```text
Admin
→ users and access configuration

Ferramentas
→ register/select canonical Tools

Job On
→ create or consult a production
→ select CM / MF / BQ
→ establish production context

Controlo Create
→ record measurements and operational control facts
→ Peso / Comparação / Pegamentos / Folha / Resumo / Definições

Controlo Approve
→ review the relevant submitted control records
→ approve / reject / reopen where the workflow allows it
→ preserve decision history

Boquilhas
→ register BQ repair movements
→ derive operational quantities and discrepancy from movement facts
→ preserve movement history
```

Each module owns its own facts. One module consuming another module's context does not transfer ownership.

---

## 4. Frontend and backend relationship

The frontend establishes the operational context of the current action.

The backend validates that context and retrieves or writes the facts required for that operation.

Important distinctions:

- a read model is not automatically a persisted entity;
- a query join is not automatically a new domain relation;
- UI grouping is not automatically database ownership;
- a DTO is not automatically a canonical identity.

A screen that needs information from several related records may compose them at read time through existing truthful relations.

---

## 5. Module map

### Admin

Admin owns application administration and access setup.

See:

- `modules/admin/OVERVIEW.md`
- `modules/admin/SETUP.md`

### Ferramentas

Ferramentas is the canonical Tool registry.

It owns Tool identity and Tool-owned master/technical facts.

See:

- `modules/ferramentas/OVERVIEW.md`
- `modules/ferramentas/CONSULTAR.md`
- `modules/ferramentas/CRIAR.md`
- `modules/ferramentas/SELECIONAR.md`
- `modules/ferramentas/VALORES_TECNICOS.md`

### Job On

Job On owns the production occurrence and establishes the selected Tool context for that production.

See:

- `modules/job-on/OVERVIEW.md`
- `modules/job-on/CRIAR.md`
- `modules/job-on/CONSULTAR.md`
- `modules/job-on/EDITAR.md`
- `modules/job-on/DUPLICAR.md`
- `modules/job-on/SELECIONAR_FERRAMENTAS.md`

### Controlo

`modules/controlo/` contains the rules and context shared by Controlo Create and Controlo Approve.

`controlo_id` is treated there as a planned evolution where applicable. It must not be spread through the model as a universal parent merely because Controlo groups several functions.

See:

- `modules/controlo/OVERVIEW.md`

### Controlo Create

Controlo Create owns the create-side operational work:

- Peso;
- Comparação;
- Pegamentos;
- Folha create-side actions;
- Resumo create-side presentation;
- Controlo Definições.

See `modules/controlo-create/`.

### Controlo Approve

Controlo Approve owns review and decision behavior where approval applies, including approval history and reopen behavior.

See `modules/controlo-approve/`.

### Boquilhas

Boquilhas preserves the existing register/movement model as the implementation base.

The current evolution is focused on movement-derived quantities and discrepancy behavior. This evolution does not, by itself, require replacing `boquilhas_id` or introducing a new repair-trace identity.

See:

- `modules/boquilhas/OVERVIEW.md`
- `modules/boquilhas/REGISTO.md`
- `modules/boquilhas/MOVIMENTOS.md`
- `modules/boquilhas/HISTORICO.md`
- `modules/boquilhas/DEFINICOES.md`

---

## 6. Controlo relationship

Controlo is one operational domain with two capability surfaces:

```text
Controlo
├── Controlo Create
└── Controlo Approve
```

The surfaces do not create two different product domains.

Common context and future shared identity rules belong in `modules/controlo/`.

Create-side behavior belongs in `modules/controlo-create/`.

Approve-side behavior belongs in `modules/controlo-approve/`.

Resumo is a read/composition surface. It does not become a persistence parent merely because other Controlo functions are displayed through it.

---

## 7. Historical truth

DMO must preserve what happened in the correct operational context.

That does not mean every module needs a dedicated history table or a new identity.

History may be represented by the durable records and relations already created by the real operational events.

Examples:

- a past Job On retains its production context;
- a decided Peso keeps its own record and decision history;
- a Comparação does not rewrite the original Peso;
- Boquilhas movement discrepancies remain historical facts and are not silently reconciled by later movements.

---

## 8. Documents and derived views

A persisted operational record, a read model and a generated PDF are different things.

```text
record ≠ read projection ≠ PDF ≠ filesystem path
```

Generated documents are derived artifacts. They do not become the authority for the underlying structured record.

Document configuration that belongs to Controlo is documented under Controlo Create → Definições.

---

## 9. How to extend the application

Before adding a field, relation, identity or persistence object, determine:

1. what real operational event creates the fact;
2. when the fact becomes known;
3. which module owns it;
4. which production/Tool/workflow context it belongs to;
5. whether an existing truthful relation already reaches it;
6. whether the requirement is persistence or only a read/composition need.

Do not create new identities or duplicate relations solely because a screen would be easier to assemble.

---

## 10. Detailed authority

For detailed behavior, use the module documents.

Start at:

- `modules/INDEX.md`

The module documents are the canonical detailed explanation of each area. This file remains intentionally compact so a person or AI can understand the application quickly before loading only the module relevant to the current task.
