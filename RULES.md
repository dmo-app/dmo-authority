# Cross-Cutting Rules

## Rule 1: Frontend without Backend (The "Pegamentos" Rule)
Some modules (specifically **Pegamentos**) currently exist as functional HTML prototypes used in real work but have **NO backend implementation**.
*   **Classification:** Working Prototype.
*   **Constraint:** Data must live exclusively in the browser session (`sessionStorage`).
*   **Prohibition:** It is **FORBIDDEN** to invent REST endpoints, database schemas, or mock APIs for these modules in `dmo-app-beta` until the backend phase is explicitly authorized.
*   **Authority:** The prototype validates UI/Flow. It does **NOT** define persistence.

## Rule 2: Explicit Human Choice
The system must never silently infer industrial decisions.
*   **Requirement:** If there are multiple valid Tools, Job Ons, or previous Pesos, the UI must force the user to click and select one.
*   **Constraint:** Even if a search returns only one result, explicit selection is required.

## Rule 3: Warnings are NOT Decisions
*   **Behavior:** Tolerance warnings, negative balances, or stale comparisons must be **displayed**.
*   **Prohibition:** The frontend must never automatically approve, reject, block, or clamp data based on a warning unless `dmo-authority` explicitly demands blocking validation.

## Rule 4: Presentation is not Domain Authority
The frontend may render and collect input, but it must not:
*   Mint canonical IDs.
*   Invent persistence relationships.
*   Implement backend-owned formulas as a second authority.
*   Turn warnings into industrial decisions.

## Rule 5: Unsaved State Preservation
When a flow temporarily leaves a form (e.g., to create a missing Tool):
*   Preserve entered values.
*   Preserve row identities.
*   Cancel returns unchanged.
*   Successful return applies *only* the explicitly selected result.
