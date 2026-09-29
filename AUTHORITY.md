# Authority & Conflict Resolution

When information conflicts, use the ownership below.

## Decision ownership

| Decision class | Authority source | Consequence |
| :--- | :--- | :--- |
| **Business rules, scope, identities, module boundaries** | **`dmo-app/dmo-authority`** | Defines the intended DMO behavior. |
| **Implementation reality** | **`dmo-app/dmo-app-beta`** | Defines what currently exists in code/schema/routes/tests. If it contradicts authority, that is implementation debt or a bug until authority is explicitly changed. |
| **Visual & interaction design** | **`dmo-app/dmo-design`** | Defines UI/layout/interaction presentation. It cannot invent domain rules, identities, persistence or backend contracts. |
| **Legacy / old repositories** | **No authority** | Historical evidence only. They never override the three repositories above. |

## Conflict rule

A newer owner-confirmed decision must be written into `dmo-app/dmo-authority` before it is treated as durable project authority.

Implementation details discovered in `dmo-app/dmo-app-beta` may be recorded here when they describe current reality, but implementation existence alone does not create a new business rule.

## Commit justification rule

Every commit that changes canonical authority content must preserve the reason for the change.

The commit message/body or the changed document must make clear:

1. **What changed** — the exact rule, identity, relation, scope statement or module behavior that changed.
2. **Why it changed** — the functional problem, owner decision, recovered context or contradiction that required the change.
3. **What supports it** — the owner-confirmed decision or evidence used to justify the change.
4. **What it does not imply** — any nearby interpretation that must not be inferred from the change, especially for identities, ownership and relationships.

A commit that only says things such as `align authority`, `update identities`, `normalize docs` or `fix context` is insufficient if it does not preserve the underlying reason.

### Identity / ownership changes

Changes involving IDs, ownership, parent-child relations, persistence boundaries or module responsibility require an explicit functional justification.

Do not introduce or promote an identity merely because:

- a screen groups several records together;
- a query would become easier;
- a field is consumed by one module;
- a table or identifier existed in an older implementation;
- a later document used the name.

The justification must state what real persistent context or fact requires the identity/relation.

### Example

For a change involving `controlo_id`, a sufficient justification would explain that Controlo requires a persistent identity for the Controlo context/sheet of one production, while also stating that this does **not** make `controlo_id` a generic parent for Peso, Comparação, Pegamentos or every other Controlo function.

The purpose of this rule is to preserve not only the final decision, but the reason that made the decision correct, so later curation or AI-assisted changes do not reinterpret it without context.
