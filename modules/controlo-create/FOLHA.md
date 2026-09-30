# Controlo — Folha

Folha participates in both Controlo Create and Controlo Approve.

## Create-side actions

- edit;
- evaluate;
- submit.

## Approve-side actions

- approve;
- reject;
- reopen.

## Families

Folha covers the families:

- CM
- BQ
- MF
- PU
- CS

## Beta context

Folha reuses the production context that already exists in the Beta Job On.

The Beta Job On intentionally contains only the production facts needed by the current Beta workflows. Folha must not force Job On to absorb additional final-application configuration merely because Folha evaluates more piece families.

Any additional data required only to complete the current Beta Folha evaluation, and not already present in the Job On context, is entered manually in Folha.

This manual entry is a **Beta scope/UX solution**, not a permanent ownership decision.

It does not redefine Folha as the canonical owner of those data, does not imply that the same data must remain Folha-owned in the complete application, and does not anticipate or prescribe any future module ownership.

If the complete application later establishes a real canonical owner for one of those facts, that ownership must be decided from the real process and represented there; the Beta manual-entry behavior must not be used as evidence against it.

This manual entry must also not be interpreted as a reason to invent new Tool identities, new Job On fields, or out-of-scope module relationships.

## Per-piece facts

Each applicable piece may carry:

- OK/NOK;
- observation;
- MCaliper link where applicable.

NOK does not automatically stop production.

OK does not automatically authorize production.

These are recorded/evaluated facts; production decisions remain explicit human actions under the owning workflow.
