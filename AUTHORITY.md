# Authority & Conflict Resolution

When decisions conflict, this hierarchy determines the winner.

## 1. Decision Ownership Matrix

| Decision Class | Authority Source | Consequence |
| :--- | :--- | :--- |
| **Global Rules & Scope** | **`dmo-authority` (This Repo)** | Wins on all business logic, module boundaries, and identities. |
| **Implementation Reality** | **`dmo-app-beta`** | Wins on *what exists today* (routes, fields, states). If code contradicts rules, it is a bug. |
| **Visual & Interaction** | **`dmo-design`** | Wins on UI/Layout. Cannot invent business rules or backend endpoints. |
| **Legacy / Old Repos** | **NONE** | Zero authority. Historical evidence only. Loses every conflict. |

## 2. The Golden Rule
If `dmo-app-beta` (Code) does something that contradicts `dmo-authority` (Rules), the Code is considered **Technical Debt** or a **Bug**, unless `dmo-authority` is explicitly updated to reflect a new business decision.
