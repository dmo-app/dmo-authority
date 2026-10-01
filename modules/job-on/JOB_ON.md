# Job On

Job On represents one concrete production occurrence and establishes the production context consumed by other modules.

This file is the single functional source for the Job On module.

## Identity and ownership

```text
jobon_id
= one production occurrence

jobon_id
├─ cm_id → selected CM tool_id
├─ mf_id → selected MF tool_id
└─ bq_id → selected BQ tool_id
```

`tool_id` remains the canonical identity of the physical Tool. `cm_id`, `mf_id` and `bq_id` identify that Tool in this production context.

The same Tool may appear in many productions. A different lot is a different Tool and therefore a different `tool_id`.

Job On owns:

- the production occurrence;
- production number/reference;
- machine;
- production date;
- selected CM/MF/BQ contexts;
- production-specific values such as TP/Calote where applicable;
- permanent Job On context-change history.

Job On does not own Tool master data, Peso, Comparação, Pegamentos, Folha, Boquilhas movements or approval facts.

## Access

Job On is one module with access capabilities.

- **Job On View**: consultation only.
- **Job On Create**: consultation plus authorized create/edit actions.

Job On Create includes the consultation needed to do its work, so it does not require an additional Job On View grant.

View/Edit screen state inside Job On Create is a UI safety behavior, not a separate permission.

An existing Job On opens safely in view state and the user explicitly enters edit mode before changing editable production information.

## Planning and consultation

The planning calendar is the normal discovery surface.

```text
calendar day
→ candidate production(s)
→ explicit user selection
→ persisted jobon_id
```

If several Job Ons exist for a date, clicking the date must not silently choose one.

A successfully saved future Job On is immediately available as planning information. This does not mean every consuming module has operationally transitioned to it.

Controlo may explicitly select a saved future Job On for preparation. Boquilhas adopts the next production according to its own production-activation rule.

## Creation

Creating a Job On creates a new `jobon_id` and the applicable production component contexts.

The operator records the production information required by the current scope and explicitly selects the exact canonical Tools.

For each selected Tool:

```text
CM tool_id → cm_id
MF tool_id → mf_id
BQ tool_id → bq_id
```

Only applicable contexts are created.

The backend allocates canonical IDs.

Creating a Job On also creates and associates the canonical Controlo production context:

```text
jobon_id
↔ controlo_id
```

That association exists from Job On creation; it is not created later by Peso, Folha, Pegamentos or Resumo.

TP/Calote is a production-specific Job On value where applicable. It does not require `tp_id` or `tampao_id`.

## Tool selection

Job On selects Tools from Ferramentas.

```text
open Ferramentas
→ search/filter candidates
→ user explicitly selects Tool
→ Ferramentas returns tool_id
→ Job On creates/resolves production context
```

If the required Tool does not exist, the user may create it through Ferramentas and then explicitly use the resulting `tool_id`.

Job On does not create its own Tool registry and must not derive identity from reference/lot/machine.

Known production information may reduce/rank candidates, but even one remaining candidate is not silently selected.

If a selected Tool is already associated with another current production/machine, the application may show an informational warning and ask for confirmation. This must not become a uniqueness constraint or automatic refusal unless a separate real business rule explicitly requires one.

## Association of pre-production records

Tool selection in Job On is also the association intent for valid pre-production records anchored to that same Tool where the owning workflow defines that transition.

Examples:

- a pre-production Peso anchored to the selected CM `tool_id` may associate to the resulting `cm_id`;
- an unresolved BQ repair trace anchored to the selected BQ `tool_id` may associate to the resulting `bq_id`.

The record keeps its own durable identity. Where the workflow defines it, the temporary direct `tool_id` anchor is cleared after association so there are not two competing canonical anchors.

No second redundant “associate?” confirmation is required after the Tool itself was explicitly selected.

## Editing and historical boundary

Editing keeps the same `jobon_id` while changing only facts explicitly editable for that production.

Tool replacement follows the operational-use boundary.

### Before operational use

If the component context is still planning-only and has not been consumed by downstream operational history, replacing its selected Tool may keep the same `cm_id`, `mf_id` or `bq_id`.

At that point the context is not yet historical operational evidence.

### After operational use

Once the component context has been operationally consumed, replacing its Tool — including changing lot — creates a new component-context identity.

```text
same jobon_id

old cm_id → old tool_id
new cm_id → replacement tool_id
```

The old context remains historical and downstream records already attached to it remain unchanged.

The same rule applies to CM, MF and BQ.

A real same-Job-On context change is permanently logged.

## Duplication

Duplicate Job On is assisted creation of a new production from an explicitly selected existing Job On.

The application must not assume the immediately previous/latest production is the correct source.

Duplication creates:

- a new `jobon_id`;
- new applicable `cm_id`, `mf_id` and `bq_id` values;
- starting selections that may reuse the source canonical `tool_id` values.

It never reuses the source production-context IDs and never modifies the source Job On.

Copied values are editable starting defaults for the new production, not frozen inheritance.

After duplication, work continues on the new Job On so the user can review/adapt it.

## Context-change awareness

Job On distinguishes planning availability, production transition and a context change inside the same production.

### Planning availability

```text
future Job On saved
→ available immediately to planning reads
→ no generic operational transition is triggered
```

### Production transition

```text
one jobon_id → another jobon_id for a machine
```

There is no global application hour at which all modules transition.

Each consuming module that uses scheduled production adoption owns its own transition rule/time. At that point it re-reads Job On and resolves the production/context that actually applies then.

An old awareness payload is not final production truth.

### Context changed

```text
same jobon_id
→ already operationally relevant context changes
→ immediate awareness
→ consumer re-reads Job On immediately
```

Examples include replacement after operational use:

```text
old cm_id → new cm_id
old mf_id → new mf_id
old bq_id → new bq_id
```

or another relevant Job On production fact such as TP/Calote where a consumer uses it.

A planning-only Tool edit before operational use does not need to create a fake urgent operational-history event.

## Change log, awareness and acknowledgement

Three concepts remain distinct:

```text
Job On change log
= permanent memory of a real same-jobon context change

awareness / ping
= tells a relevant consumer to revalidate

acknowledgement
= seen / taken notice of
```

Acknowledgement does not mean corrected, recalculated, approved, resolved or production-transition-applied.

Acknowledgement never deletes the permanent Job On change history.

Awareness is lightweight. It must not become a duplicated Job On snapshot.

Awareness follows the actual application functions that consume the changed context. Do not create a separate manually maintained CM/MF/BQ/TP routing catalogue merely for notifications.

Historical records remain on the original context where they were created; awareness never retargets them to a replacement context.

## Machine changes

A machine reassignment in planning is not automatically an urgent CM/MF/BQ context-change event.

If a workflow needs special immediate behavior for machine reassignment, that rule must be explicit rather than inferred from production-transition behavior.

## Rules that must not be violated

An implementation must not:

- reuse source `jobon_id`, `cm_id`, `mf_id` or `bq_id` when duplicating a production;
- silently choose a Job On or Tool candidate;
- create Tool identity inside Job On;
- mutate an operationally consumed component context to another Tool;
- retarget historical downstream records after a Tool/context replacement;
- create a new component-context ID unnecessarily for a planning-only replacement before operational use;
- treat saving a future Job On as a generic operational production transition;
- hardcode midnight, 07:00 or another universal transition time for every module;
- delay an already-operational same-jobon context change until a later scheduled transition;
- interpret acknowledgement as resolution/approval/correction;
- delete the permanent change log when awareness is acknowledged;
- duplicate a full Job On snapshot into awareness without a separate justified need;
- create a second notification-routing domain instead of following the functions that already consume the context.
