# Beta Scope Boundaries

**Definition:** The Beta is a **Scope-Reduced** version of the DMO application.
Modules listed in "OUT" are strictly forbidden.

## 🟢 IN SCOPE (The Beta Product)
1.  **Identity & Access:** Admin, Users, Templates.
2.  **Tools:** Canonical `tool_id` catalog (CM, MF, BQ).
3.  **Job On (Light):** Production planning, context creation (`jobon_id`, `cm_id`, `mf_id`, `bq_id`).
4.  **Controlo:**
    *   **Peso:** Measurement, Calculation, Submission.
    *   **Pegamentos:** Measurement (Status: **Frontend Prototype**).
    *   **Approve:** Review and Decision.
    *   **Resumo:** Production entry point.
5.  **Boquilhas:** External repair aggregate, movements, history.

## 🔴 OUT OF SCOPE (Strictly Forbidden)
*   **Armazém:** Stock, locations, logistics.
*   **Reparação Interna/Programada:** Complex workshop flows.
*   **Tampões:** Independent module.
*   **Full Tool Lifecycle:** Verification, reset, deactivation.
*   **History:** As a separate browseable module (History is accessed via context, not top-level nav).

## 📊 Implementation Status Matrix

| Module | Scope Status | Backend (`dmo-app-beta`) | Frontend (`dmo-design`) |
| :--- | :--- | :--- | :--- |
| **Pegamentos** | 🟢 IN | 🔴 **NOT IMPLEMENTED** | 🟡 **Working Prototype** (Session-only) |
| **Boquilhas** | 🟢 IN | 🔴 **NOT IMPLEMENTED** | 🔵 **Target** |
| **Job On** | 🟢 IN | 🟢 **Implemented (Light)** | 🟢 **Target** |
| **Controlo (Peso)** | 🟢 IN | 🟢 **Implemented** | 🟢 **Target** |
| **Controlo (Approve)**| 🟢 IN | 🔴 **NOT IMPLEMENTED** | 🔵 **Target** |
