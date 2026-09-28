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
