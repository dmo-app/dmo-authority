# Controlo — Folha

Folha is one shared Controlo surface/state exposed through both Controlo Create and Controlo Approve.

There is not a separate "Folha Create" and "Folha Approve" copy.

## Capability behavior

### Controlo Create

Create may:

- read;
- edit;
- evaluate;
- submit.

Changes made through Create become part of the same underlying Folha/control state that Approve later reads.

### Controlo Approve

Approve reads the same Folha/control state.

It does **not** edit the operational Folha content.

Approve may perform the approval-side actions defined by the approval workflow:

- approve;
- reject;
- reopen where permitted.

Those actions persist approval decisions/history; they do not give Approve permission to change Create-owned Folha fields, measurements, observations or technical facts.

Conceptually:

```text
Create edits Folha/state
-> same underlying state changes
-> Approve sees the updated state

no copied Folha
no second Folha
no approval-side editing of operational content
```

## Families

Folha covers the families:

- CM
- BQ
- MF
- PU
- CS

## Current-scope context

Folha reuses the production context defined by the current-scope Job On.

Job On contains only the production facts owned by its current-scope workflows. Folha must not force Job On to absorb additional configuration merely because Folha evaluates more piece families.

Any additional data required only to complete the current-scope Folha evaluation, and not already present in the Job On context, is entered manually in Folha.

This manual entry is a **current-scope UX solution**, not a permanent ownership decision.

It does not redefine Folha as the canonical owner of those data, does not imply that the same data must remain Folha-owned in the complete application, and does not anticipate or prescribe any future module ownership.

If the complete application later establishes a real canonical owner for one of those facts, that ownership must be decided from the real process and represented there; the current-scope manual-entry behavior must not be used as evidence against it.

This manual entry must also not be interpreted as a reason to invent new Tool identities, new Job On fields, or out-of-scope module relationships.

## Per-piece facts

Each applicable piece may carry:

- OK/NOK;
- observation;
- MCaliper link where applicable.

NOK does not automatically stop production.

OK does not automatically authorize production.

These are recorded/evaluated facts; production decisions remain explicit human actions under the owning workflow.
