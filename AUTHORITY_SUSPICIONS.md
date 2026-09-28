# Authority Suspicions

This file records statements found during authority curation that are not safe to promote as canonical.

Nothing in this file is authority by itself.

---

SUSPICION_ID: AS-001
STATEMENT: A future persisted `resumo_id` record is canonical for Resumo.
SOURCE: `HOW_THE_APP_WORKS.md`
LOCATION: Former section "The persisted Resumo record (future)" removed during the 2026-09-28 curation pass.
WHY_SUSPICIOUS: `resumo_id` is explicitly a suspicion term. The same document defines the current Resumo as a read projection assembled from `jobon_id` and states that function records do not need a `resumo_id` parent.
CLASSIFICATION: OWNER_CONFIRMATION_REQUIRED
CURRENT_AUTHORITY_SUPPORT: No retained canonical statement after this curation pass.
CONFLICTING_EVIDENCE: Resumo is described elsewhere in `HOW_THE_APP_WORKS.md` as a dashboard/read composition over existing production relations, with no parent-child FK requirement.
OWNER_DECISION_REQUIRED: Decide whether Resumo must remain only a read projection or whether a separate persisted Resumo identity is genuinely required.
RECOMMENDED_ACTION: Keep `resumo_id` out of canonical identities and persistence rules until the owner explicitly confirms it.

---

SUSPICION_ID: AS-002
STATEMENT: `controlo_id` is already a canonical persistent Controlo production-level identity.
SOURCE: `IDENTITIES.md`
LOCATION: Former section "controlo_id — Controlo production-level node" removed during the 2026-09-28 curation pass.
WHY_SUSPICIOUS: `controlo_id` is explicitly subject to suspicion when wording conflicts. The removed section called it a canonical recent decision, while `HOW_THE_APP_WORKS.md` also emphasizes that the Controlo UI hierarchy must not be mirrored as a database parent and that existing natural anchors remain meaningful.
CLASSIFICATION: OWNER_CONFIRMATION_REQUIRED
CURRENT_AUTHORITY_SUPPORT: The current retained authority does not promote `controlo_id` as a canonical identity.
CONFLICTING_EVIDENCE: The architecture requires minimal truthful relations and rejects creating parent identities merely because multiple functions appear under Controlo. This does not by itself disprove `controlo_id`, but it prevents inferring its exact role.
OWNER_DECISION_REQUIRED: Confirm whether `controlo_id` exists as a durable identity and, if yes, define its exact purpose and relationship to `jobon_id` without making it a generic parent for Peso, Comparação, Pegamentos, Folha, or Resumo.
RECOMMENDED_ACTION: Keep `controlo_id` outside `IDENTITIES.md` until the owner decision is explicit and unambiguous.

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
