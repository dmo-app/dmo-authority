# Rules

## Rule 1: Frontend without Backend (Local Prototypes)

Some surfaces (specifically **Pegamentos**, a sub-surface of Controlo Create) currently exist as local HTML prototypes used in real work, but have NO backend implemented yet.

- These are classified as "Working Prototypes".
- Data must live exclusively in the browser session (`sessionStorage`).
- Browser-generated IDs are FORBIDDEN. The prototype must not mint identifiers that will later collide with backend-allocated canonical IDs.
- Client-owned formulas are FORBIDDEN. The prototype must not calculate tolerance, ovality, capacity or any industrial result as a second authority.
- It is strictly FORBIDDEN to invent REST endpoints, create database schemas, or mock APIs for these prototypes until the backend phase officially begins.
- The prototype serves to validate UI/flow, not to define persistence.
- When the backend is implemented, it follows the rules in this repo, NOT the structure of the HTML prototype.

## Rule 2: Explicit Human Choice

The system must never silently infer industrial decisions.

- If there are multiple valid Tools, Job Ons, or previous Pesos, the UI must force the user to click and select one.
- **Even if a search returns only one result, explicit selection is required.** Auto-selection of a single candidate is forbidden.
- Pre-population is assistance only: known context (reference, machine, expected type) may pre-fill search criteria, but unknown Tool facts are never invented.
- The frontend must never infer Tool identity from reference + lot + machine labels.

## Rule 3: Warnings are not Decisions

Tolerance warnings, negative balances, stale comparisons and threshold alerts must be displayed to the user.

- The frontend must never automatically approve, reject, block, or clamp data based on a warning unless this repo explicitly demands blocking validation.
- **Approve never rejects on warnings and reject never auto-triggers**: status changes only per an explicit human action on the route.
- Decision commands carry no warning/result input and no code path derives a decision from calculation results.
- A warning is a gate that a human acknowledgement opens; it is never a hard prohibition and never a stored state.

## Rule 4: Presentation is not Domain Authority

The frontend may render, collect input, preserve local draft state and orchestrate published actions.

It must not:
- Mint canonical IDs.
- Invent persistence relationships.
- Infer Tool identity from labels or displayed text.
- Implement backend-owned formulas as a second authority.
- Turn warnings into industrial decisions.
- Synthesize actor/time/audit facts.
- Infer permissions from role/profile labels.
- Create production APIs from fixtures.
- Treat display text as identity.

## Rule 5: Unsaved State Preservation

When a flow temporarily leaves a form to select/create a Tool or repair missing context:

- Preserve entered values.
- Preserve row identities.
- Cancel returns unchanged.
- Successful return applies only the explicitly selected/created result.
- The origin token is optional, opaque and echoed verbatim on every event; never parsed, trimmed, normalized, interpreted or embedded in a URL; never a canonical identity.

## Rule 6: Surgical Queries / Light Packets (Standing Rule)

Queries and read models must be context-specific and carry only the data the surface needs.

- No global scans.
- No loading whole relations/tables to filter or project in the frontend.
- No "load the universe and filter client-side".
- Backend reads are built per query contract with the smallest packet that satisfies the surface.
- History is only loaded when the operator explicitly asks for it.

This rule binds every remaining and future Beta backend work.

## Rule 7: No Lifecycle State Machine

No Job On-wide status, stage, phase or state machine exists.

- Specifically absent: `rascunho`, `planeado`, `em fabrico`, `fechado`, `cancelado`, `active`, `locked`, `approved` (as lifecycle states).
- Peso status vocabulary is exactly three values: `pendente` / `aprovado` / `nao_aprovado`.
- No edit changes any status. Status transitions happen only through explicit human decision actions.
- Warnings stay warnings; they never become stored lifecycle states.

## Rule 8: Backend owns IDs and Attribution

- `peso_id`, `tool_id`, `jobon_id`, `cm_id`, `mf_id`, `bq_id` are created only by the backend inside the create transaction.
- No client ever supplies or guesses canonical IDs.
- Actor/time are backend facts (`ICurrentAccountContext`, backend clock) — never client fields.
- No client-supplied actor/time exists anywhere.
- Audit trail never invents actor/time.

## Rule 9: Concurrency Discipline

- No silent retry, no last-write-wins, no automatic merge.
- One version increment per committed mutation; reads never bump.
- A refused mutation reports the typed reason and the surface reloads (`conflict` presentation).
- A stale edit writes nothing.
- Every guarded write carries `ExpectedVersion`; the database backstop and the application validator raise the same typed refusal.

## Rule 10: Module Boundaries

- **Controlo Create and Controlo Approve remain distinct modules.** No generic architecture may force their workflows/pages to be identical.
- A module owns its own workflow, rules and module-specific persistence.
- Modules must not reach directly into another module's internal tables or implementation.
- Preferred direction: `Controlo → shared contract / context provider → backend`.
- Forbidden: `Peso service → arbitrary Job On tables → arbitrary Armazém tables → arbitrary Boquilhas tables`.
- Modules meet through stable shared identities (`tool_id`, user/access identities, production context) or through explicit contracts.

## Rule 11: Fixed Desktop Layout

DMO is a fixed-layout desktop operational application, not a responsive public website.

- Canonical design and validation viewport: **1366 × 768**.
- Compact density; region-stable composition.
- Breakpoint-driven structural reflow is prohibited.
- No `@media` / `@container` / `@supports` structural rules in module CSS.
- Mobile and tablet layouts are out of scope.
- Local keyboard-reachable overflow for wide regions; scrolling is handled by the layout, not by script.

## Rule 12: HISTÓRICO LOCAL ≠ HISTÓRICO GLOBAL

- **HISTÓRICO LOCAL**: cross-cutting history capability that belongs to each module (e.g. "Histórico de Pesos" inside Controlo Approve). Every Beta module retains its local HISTÓRICO requirement.
- **HISTÓRICO GLOBAL** (technical identity `historia`): DEFERRED BY DESIGN in this Beta. No route, no availability, no top-level navigation entry. Its identity is preserved in the catalog but never exposed.
- The local Histórico never aggregates other modules' histories.

## Rule 13: No Duplication of Truth

- Do not store Job On production facts (reference, production_number, machine, production_date, processo) on the Resumo — they stay Job On truth and are traversed.
- Do not store Tool nominal/lot/quantity — Tool truth.
- Do not store Folha's decisions/observations — Folha truth; Resumo must not project Folha.
- Do not store Peso status/attribution on other records — Peso truth.
- PDF bytes/filename/path are derived output, not stored identity.
- No convenience column duplicating another module's truth.

## Rule 14: Ferramentas is Contextual

- Ferramentas is a shared contextual flow, not a top-level Beta module screen.
- Do not invent a top-level Ferramentas destination.
- The origin module never becomes Tool owner: neither Job On nor Controlo nor Boquilhas writes `tools` outside the Tool create command.
- Tool create returns the canonical `tool_id` to the consumer (never to a shared primitive).
- No per-module Tool registry; one Tool registry only.

## Rule 15: Record ≠ PDF ≠ File Path

- `database record != generated PDF != file path`.
- Use the planned final directory convention from the start:
