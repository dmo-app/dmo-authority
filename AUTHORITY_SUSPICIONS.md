# Authority Suspicions

This file records statements found during authority curation that are not safe to promote as canonical.

Nothing in this file is authority by itself.

Resolved suspicions are removed once the owner decision has been promoted into canonical authority; Git history preserves the prior curation record.

---

SUSPICION_ID: AS-003
STATEMENT: Migration numbers, current route exposure, and current implementation completeness belong in Beta scope authority.
SOURCE: `SCOPE.md`
LOCATION: Former section "Current implementation reality" removed during the 2026-09-28 curation pass.
WHY_SUSPICIOUS: Migration counts, route registration state, and implementation completeness are volatile implementation evidence. They can become stale without any change to business scope.
CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC
CURRENT_AUTHORITY_SUPPORT: The IN/OUT scope itself remains canonical. Current implementation state does not.
CONFLICTING_EVIDENCE: `AUTHORITY.md` assigns implementation reality to `dmo-app/dmo-app-beta`, while this repository owns durable business rules, scope, identities, and module boundaries.
OWNER_DECISION_REQUIRED: None.
RECOMMENDED_ACTION: Keep implementation status in implementation evidence/reviews, not in `SCOPE.md`.

---

SUSPICION_ID: AS-004
STATEMENT: A prototype must persist its data specifically in browser `sessionStorage`.
SOURCE: `RULES.md`
LOCATION: Former Rule 1 "Frontend without Backend (Local Prototypes)" removed/re-written during the 2026-09-28 curation pass.
WHY_SUSPICIOUS: Browser persistence is a prototype mechanic, not a durable domain rule. Promoting it would turn a temporary implementation choice into authority.
CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC
CURRENT_AUTHORITY_SUPPORT: The durable boundaries were retained: prototypes do not define canonical IDs, persistence, backend contracts, or backend-owned calculations.
CONFLICTING_EVIDENCE: The repository's authority-writing rules exclude demo mechanics and code-specific workarounds.
OWNER_DECISION_REQUIRED: None.
RECOMMENDED_ACTION: Keep only the durable prototype boundary in authority; leave storage mechanism to design/implementation evidence.

---

SUSPICION_ID: AS-005
STATEMENT: Current backend/UI completion state and specific migration references belong in `HOW_THE_APP_WORKS.md`.
SOURCE: `HOW_THE_APP_WORKS.md`
LOCATION: Former "Implementation status" sections for Comparação, Pegamentos, and Folha removed/re-written during the 2026-09-28 curation pass.
WHY_SUSPICIOUS: These statements describe a momentary code state rather than how the domain works. They can become false while the functional rule remains unchanged.
CLASSIFICATION: SUPPORTED_BUT_IMPLEMENTATION_SPECIFIC
CURRENT_AUTHORITY_SUPPORT: Durable workflow and persistence-boundary statements remain.
CONFLICTING_EVIDENCE: `AUTHORITY.md` assigns implementation reality to `dmo-app/dmo-app-beta`.
OWNER_DECISION_REQUIRED: None.
RECOMMENDED_ACTION: Keep transient implementation status out of authority documents; retain only durable workflow and persistence constraints.
