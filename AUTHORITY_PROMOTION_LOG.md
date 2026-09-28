# Authority Promotion Log

This log records authority-curation actions. It is not itself a source of business canon.

## 2026-09-28 — Curation pass

### Promoted / retained as durable authority

**Prototype boundary**
- SOURCE USED: Existing `RULES.md` prototype rule.
- PROMOTED/RETAINED: Prototypes may validate presentation and interaction but do not define persistence, canonical identities, backend contracts, or backend-owned industrial calculations.
- WHY SAFE: This is implementation-independent and preserves the separation between design/prototype behavior and domain authority.

**Minimal persistence boundary for Pegamentos and Folha**
- SOURCE USED: Current owner-confirmed `HOW_THE_APP_WORKS.md`.
- PROMOTED/RETAINED: Persistence must be derived from minimum durable facts and truthful existing relations; UI structure does not automatically imply a new identity/table or parent-child relationship.
- WHY SAFE: This is a durable architectural/domain constraint rather than a statement about current code completeness.

**Beta scope**
- SOURCE USED: Existing `SCOPE.md`.
- PROMOTED/RETAINED: The IN/OUT capability boundary remains authority.
- WHY SAFE: Scope is durable product intent and belongs in this repository.

### Rejected / demoted from authority

**Current implementation table in `SCOPE.md`**
- REJECTED: Migration number, implementation completeness, route exposure, and `ModuleRegistrations.CurrentBuildAvailable` state.
- CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC.
- ACTION: Removed from `SCOPE.md`.
- WHY: These belong to `dmo-app/dmo-app-beta` implementation evidence and can become stale independently of scope.

**Prototype `sessionStorage` requirement**
- REJECTED: Browser storage mechanism as authority.
- CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC.
- ACTION: Removed from `RULES.md`; durable prototype boundaries retained.
- WHY: Storage mechanism is demo/implementation behavior, not domain canon.

**Implementation-status blocks in `HOW_THE_APP_WORKS.md`**
- REJECTED: Current migration/UI/backend-completeness statements.
- CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC.
- ACTION: Removed or rewritten as durable persistence boundaries.
- WHY: Authority must describe how DMO works, not the transient completion state of the codebase.

**Implementation-presence annotations in `IDENTITIES.md`**
- REJECTED: Statements that an identity is "present in dmo-app-beta" as part of identity authority.
- CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC.
- ACTION: Removed.
- WHY: Whether code currently implements an identity does not determine whether that identity is canonical.

### Unresolved — not promoted

**`resumo_id`**
- SOURCE: Former future-persistence section in `HOW_THE_APP_WORKS.md`.
- CLASSIFICATION: OWNER_CONFIRMATION_REQUIRED.
- ACTION: Removed from canonical prose and recorded in `AUTHORITY_SUSPICIONS.md` / `OPEN_DECISIONS.md`.
- WHY NOT PROMOTED: `resumo_id` is explicitly suspicious and current safe authority already supports Resumo as a read composition without making function records children of a summary identity.

**`controlo_id`**
- SOURCE: Former section in `IDENTITIES.md`.
- CLASSIFICATION: OWNER_CONFIRMATION_REQUIRED.
- ACTION: Removed from canonical identities and recorded in `AUTHORITY_SUSPICIONS.md` / `OPEN_DECISIONS.md`.
- WHY NOT PROMOTED: Its existence and exact durable purpose require an explicit owner decision; UI containment and implementation convenience are insufficient evidence.

### Files changed in this pass

- `SCOPE.md`
- `RULES.md`
- `IDENTITIES.md`
- `HOW_THE_APP_WORKS.md`
- `AUTHORITY_SUSPICIONS.md` (created)
- `OPEN_DECISIONS.md` (created)
- `AUTHORITY_PROMOTION_LOG.md` (created)
