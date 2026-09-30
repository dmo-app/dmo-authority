# Implementation Slices

This directory is the **fresh-start implementation layer of the DMO application context**, derived from the current canonical blueprint.

It is a backlog, but it is not a detached annex. When a Developer works on a module, the associated implementation slice(s) are part of the normal context package for that work.

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

## Place in the application context

The normal implementation reading path is:

```text
HOW_THE_APP_WORKS.md
→ relevant module canon
→ associated implementation slice(s)
→ IMPLEMENTATION_STATUS.md when runtime evidence matters
```

This directory explains bounded construction work. It does not redefine the product.

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


## Canonical association map

Every implementation file must be read together with the canonical blueprint that owns the behavior.

This map is the discovery bridge between `features-to-implement/` and the current product canon. It does **not** make every listed file READY.

| Implementation file | Canonical owner / source | Current use |
| --- | --- | --- |
| `ACCESS_TEMPLATES_BLUEPRINT_COMPLETION.md` | `modules/admin/OVERVIEW.md`, `USERS.md`, `TEMPLATES.md`, `APP_DEFINICOES.md` | Admin implementation/alignment draft; reconcile recovery wording before Developer use. |
| `BOQUILHAS_TRACE_IMPLEMENTATION.md` | `modules/boquilhas/OVERVIEW.md`, `REGISTO.md`, `MOVIMENTOS.md`, `HISTORICO.md`, `IDENTITIES.md` | Repair-trace implementation work. The ambiguous multiple-unresolved-trace case remains blocked by the owning canon. |
| `COMPARACAO_UI_COMPLETION.md` | `modules/controlo-create/COMPARACAO.md` | Older UI-completion framing; use the canonical Comparação workflow as authority and reconcile before fresh-start implementation. |
| `CONTROLO_CONTEXT_IMPLEMENTATION.md` | `modules/controlo/OVERVIEW.md`, `modules/job-on/CRIAR.md`, `modules/controlo-create/RESUMO.md`, `IDENTITIES.md` | Older recovery framing for the shared Controlo context; current canon owns the `jobon_id ↔ controlo_id` behavior. Reconcile before Developer use. |
| `FOLHA_PERSISTENCE_IMPLEMENTATION.md` | `modules/controlo-create/FOLHA.md`, `modules/controlo/OVERVIEW.md`, `modules/controlo-approve/OVERVIEW.md` | Persistence/evaluation draft. Canonical Folha ownership/lifecycle detail must be sufficient before implementation proceeds. |
| `FOUNDATION_RUNTIME_GAPS.md` | `IMPLEMENTATION_STATUS.md` plus the owning functional documents for each affected area | Volatile implementation/runtime checklist, not product canon and not a durable functional slice. Re-verify before use. |
| `JOB_ON_CONTEXT_CHANGE_AWARENESS.md` | `modules/job-on/CONTEXT_CHANGE_AWARENESS.md` plus the consuming module documents | Cross-cutting implementation slice. Timing semantics are canonical; exact consumer dependency mapping must come from the owning workflows. |
| `JOB_ON_DUPLICATION_ALIGNMENT.md` | `modules/job-on/DUPLICAR.md` | Job On duplication implementation/alignment slice. |
| `PEGAMENTOS_BACKEND_IMPLEMENTATION.md` | `modules/controlo-create/PEGAMENTOS.md` | BLOCKED until the Pegamentos identity/cardinality/lifecycle/tolerance/applicability questions listed in the slice are closed in canon. |
| `PESO_HISTORICAL_DIFFERENCE.md` | `modules/controlo-create/PESO.md`, with global explicit-choice/query rules in `RULES.md` | Fresh-start implementation slice for the normal Peso historical-difference flow. |
| `PESO_TECHNICAL_VALUES_ALIGNMENT.md` | `modules/controlo-create/PESO.md`, `modules/ferramentas/VALORES_TECNICOS.md` | Older recovery/alignment framing; depends on the Tool technical-values contract and must be reconciled before Developer use. |
| `PROTOTYPE_FAKE_BACKEND_REWORK.md` | `RULES.md`, `HOW_THE_APP_WORKS.md`, and the relevant module canon | Prototype-support work only. It must never be used as a production backend/API/schema contract. |
| `SETUP_MODE_PROVIDER_CONNECTION.md` | `modules/admin/SETUP.md`, `modules/admin/OVERVIEW.md`, `RULES.md` | Setup/infrastructure slice; technical provider validation may still block implementation details. |
| `TOOL_TECHNICAL_VALUES_IMPLEMENTATION.md` | `modules/ferramentas/OVERVIEW.md`, `modules/ferramentas/VALORES_TECNICOS.md`, `IDENTITIES.md` | BLOCKED until the units/domain/applicability/edit-capability decisions listed in the slice are closed in canon. |

Reading rule:

```text
implementation file
→ load its canonical owner/source
→ check whether the slice is READY or BLOCKED
→ if wording conflicts, canonical owner wins
→ if the slice still uses recovery/baseline framing, reconcile it before handing it to a Developer
```

An implementation filename is therefore a pointer to work, not proof that the product contract is complete.

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
