# Folha Persistence / Evaluation Implementation

**Status:** FUNCTIONAL BLUEPRINT DEFINED — PERSISTED EVALUATION LAYER MISSING IN INSPECTED BASELINE

**Type:** Controlo Create + Approve feature implementation

## Functional source

Implementation must follow `modules/controlo-create/FOLHA.md` and the shared Controlo rules.

Folha participates in both capability surfaces.

Create-side actions:

- edit;
- evaluate;
- submit.

Approve-side actions:

- approve;
- reject;
- reopen.

## Families

Folha covers:

- CM;
- BQ;
- MF;
- PU;
- CS.

Each applicable piece may carry:

- OK/NOK;
- observation;
- MCaliper link where applicable.

OK/NOK is recorded/evaluated information. It does not automatically authorize or stop production.

## Beta boundary

Folha reuses the production context already present in the Beta Job On.

The Beta must not expand Job On with final-application fields merely because Folha needs additional evaluation data.

Additional data required only for the current Beta Folha may be entered manually in Folha.

That manual-entry behavior is a Beta scope/UX solution. It does not establish permanent ownership for the full application.

## Shared Controlo context interaction

The Folha implementation must be designed together with the pending `controlo_id` shared-context work.

Do not assume that every Folha-visible value must be copied into `controlo_id`.

Use the real owner/relation where historical reconstruction remains truthful. Persist/freeze only facts that genuinely require Controlo-owned historical stability.

## Reviewer checks

Reject an implementation that:

- expands Job On merely to host Beta-only Folha input;
- treats Folha manual entry as permanent full-application ownership;
- turns OK/NOK into automatic production approval/rejection;
- creates new Tool identities to satisfy missing Folha inputs;
- duplicates stable Job On/Tool truth without a historical reason.
