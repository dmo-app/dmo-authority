# Access Templates Blueprint Completion

**Status:** BLUEPRINT COVERAGE INCOMPLETE — IMPLEMENTATION MUST NOT PROCEED FROM ASSUMPTIONS

**Type:** Product-definition prerequisite

## Problem

`IMPLEMENTATION_STATUS.md` records that Access Templates coverage is still incomplete.

Before implementation relies on Templates, the current product behavior must be captured explicitly in the blueprint.

## Boundary already known

Access templates belong to the access/admin model.

Email templates used by Controlo belong to the relevant Controlo configuration workflow.

The shared word "template" does not create one generic Templates product domain.

## Required work

Before coding/reworking Access Templates:

1. recover/confirm the actual current product behavior;
2. document ownership, purpose, permissions and lifecycle;
3. identify what is Beta scope;
4. distinguish access templates from demonstration/prototype storage and historical profile concepts;
5. only then compare implementation reality against the completed blueprint.

## Reviewer checks

Reject implementation work that:

- treats old demo templates as canon because they exist;
- imports historical profile/role concepts without current confirmation;
- merges access templates and email templates solely because both are called templates;
- fills blueprint gaps from implementation accidents.
