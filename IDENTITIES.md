# Canonical Identities

## 1. `tool_id` (The Master)
*   **Definition:** The generic, canonical identity of any Tool (CM, MF, BQ).
*   **Rule:** It is not owned by any specific module.
*   **Constraint:** A different lot is a **DIFFERENT** `tool_id`.

## 2. `jobon_id` (The Context)
*   **Definition:** The production occurrence context.
*   **Business Key:** `reference` + `production_number`.
*   **Rule:** Created per production. Modules enrich this ID, they do not replace it.

## 3. `cm_id`, `mf_id`, `bq_id` (The Snapshots)
*   **Definition:** Historical snapshots of a `tool_id` within a specific `jobon_id`.
*   **Rule:** They are **NOT** new tools. They are *photographs* of the tool at that moment.
*   **Constraint:** The Frontend must never mint these IDs.

## 4. `bq_repair_trace_id` (The Aggregate)
*   **Definition:** The identity of a specific repair session/aggregate for Boquilhas.
*   **Rule:** Distinct from `tool_id`. Tracks the physical nozzle in repair flow.
