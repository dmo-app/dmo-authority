# DMO Beta — How the App Works

---

## 1. WHAT DMO IS

DMO is an operational factory application for glass container manufacturing. It connects the physical reality of the shop floor — tools, productions, measurements, decisions — into a single coherent system where every fact is recorded in the context where it actually happened.

The system does not revolve around one central data object. It revolves around **production context**: what was produced, with which tools, measured how, decided by whom, and what must be remembered later.

DMO connects:

- **Tools** — physical moulds and components (CM, MF, BQ) that exist over years and are used across many productions.
- **Productions** — individual manufacturing runs (Job Ons), each using specific tools at a specific point in time.
- **Operational modules** — Controlo (quality measurement and decision), Boquilhas (BQ repair movements), Ferramentas (tool identity), Admin (users and access).
- **Measurements** — water weight, dimensional checks, overlap verification, evaluation results.
- **Decisions** — approvals, rejections, comparisons, evaluations.
- **Historical context** — the ability to return to any past production and see exactly what was true at that time.

The guiding principle: **persist reality, compose context at query time.** The database stores facts and truthful durable relations. The frontend workflow establishes the current operational question. The backend validates that context, traverses existing relations with focused queries, and returns only what the current screen needs.

---

## 2. THE BASIC CONTEXT CHAIN

The entire system is built on a small chain of identities that express reality:

```
tool_id          → the physical Tool identity over its entire lifetime
jobon_id         → one production occurrence
cm_id / mf_id / bq_id → that physical Tool in that specific production context
```

### tool_id — the physical thing

A `tool_id` answers: "Which physical tool is this?" It is created once when the tool is registered in Ferramentas. It carries stable master facts: type (CM/MF/BQ), reference, lot, processo, machine compatibility. It does not carry production-specific values, measurement results, or module-specific facts. It is not a container for everything that ever happened to the tool.

### jobon_id — one production occurrence

A `jobon_id` answers: "Which production run is this?" It is created when a user with **Job On Create** plans a production. It carries: reference, production number, machine, production date. It is the anchor for everything that happens during that production.

### cm_id / mf_id / bq_id — the tool frozen in context

When a Job On selects a tool, the system creates a **context snapshot**: a `cm_id` (for CM), `mf_id` (for MF), or `bq_id` (for BQ). This snapshot records which physical tool (`tool_id`) was used and freezes the relevant tool state at that moment (type, reference, lot).

**Why snapshots?** Because the physical tool will change over time. Its lot may be updated, its state may change, its processo may be corrected. But production A used the tool as it was on that day. The snapshot preserves that truth. When someone opens production A two years later, they see what was actually used — not the tool's current state.

**Why new context IDs per production?** Because each production is a distinct historical event. The same physical CM tool (same `tool_id`) used in production A and production B gets two different `cm_id` values — one for each production. This keeps the historical record clean: production A's `cm_id` freezes the tool state as it was during A; production B's `cm_id` freezes it as it was during B.

### Concrete example

Physical CM tool `T-5447` exists. It is registered in Ferramentas with `tool_id = 7a3f…`.

- **Production A** (January): Job On `J-2026-001` selects this tool. The system creates `cm_id = c1a2…` → `jobon_id = J-2026-001` + `tool_id = 7a3f…`, freezing lot "3" and reference "5447".
- Between productions, the lot is updated to "4" in Ferramentas.
- **Production B** (March): Job On `J-2026-042` selects the same tool. The system creates `cm_id = c9d8…` → `jobon_id = J-2026-042` + `tool_id = 7a3f…`, freezing lot "4" and reference "5447".

Both `cm_id` values point to the same `tool_id`. But they preserve different historical states. Production A always shows lot "3". Production B always shows lot "4". Changing the tool's current state in Ferramentas never rewrites either production's history.

---

## 3. HOW THE FRONTEND AND BACKEND WORK TOGETHER

DMO does not assume the backend must pre-consolidate the complete domain state before the UI can operate. The frontend workflow already knows the operational context. The backend validates it and retrieves only what is needed.

### The normal pattern

```
The operator is in a specific module, looking at a specific production,
performing a specific operation.

The frontend knows:
  - which module (Controlo, Boquilhas, Job On…)
  - which production (jobon_id)
  - which component/context (cm_id)
  - which operation (create Peso, start Comparação, open Resumo…)
  - which screen state (draft, submitted, read-only…)

The frontend sends a contextual request:
  "I need Peso data for peso_id X" or
  "I need the Controlo summary for jobon_id Y"

The backend validates the context:
  - Does this peso_id exist?
  - Does this cm_id belong to this jobon_id?
  - Is the user authorized for this module?

The backend performs a focused Dapper query:
  - Traverses only the relations required for THIS operation
  - Filters at query time
  - Projects a purpose-specific DTO

The backend returns the focused read model.

The page renders.
```

### Four critical distinctions

These are not stylistic preferences. They prevent the persistence model from being shaped by read convenience.

**READ MODEL ≠ ENTITY.** A DTO assembled for one screen is not a new entity. It has no lifecycle, no concurrency boundary, no independent identity. The `PesoSheetReadModel` used by both Controlo Create and Controlo Approve is a read projection, not a persisted record.

**QUERY JOIN ≠ DOMAIN RELATION.** Joining `pesos` to `cm_contexts` to `jobons` in a query does not mean `pesos` needs a `jobon_id` foreign key. The join traverses an existing true relation: `peso_id → cm_id → jobon_id`. The database already expresses this connection. The query uses it.

**UI GROUPING ≠ DATABASE OWNERSHIP.** The Controlo navigation shows Resumo, Peso, Comparação, Pegamentos, Folha together. This is a UI grouping. It does not mean these records need a shared parent FK. They are grouped in the interface because they belong to the same operational module. They are connected in the database through their real context relations (component contexts, Job On).

**DTO ≠ DOMAIN IDENTITY.** A purpose-built projection returned by a Dapper query exists for the duration of one request. It is not a persisted aggregate. It does not need an identity, a table, or a lifecycle.

### Peso example

The operator opens Peso for a specific CM in a specific production. The frontend sends `peso_id` (or `cm_id` for a new Peso). The backend performs a focused read:

```
peso_id → pesos row (measurements, status, frozen density)
       → cm_contexts row (which CM, which production)
       → tools row (tool identity, reference, lot)
       → peso_measurement_rows (individual readings)
```

This is one focused query. It does not load the tool's entire production history. It does not join Pegamentos, Folha, or Boquilhas. It retrieves exactly what the Peso page needs.

### Resumo example

A user with access to **Controlo** opens the Resumo for a production. The frontend sends `jobon_id`. The backend performs a different, broader query:

```
jobon_id → job_ons row (production facts)
        → cm_contexts / mf_contexts / bq_contexts (component contexts)
        → pesos (Peso status, results)
        → comparacoes (Comparação state, if any)
        → [pegamentos, folha — when implemented]
```

This is a different query with a different shape and a different DTO. It assembles the dashboard view at query time. **No function record needs a `resumo_id` FK.** The dashboard traverses existing relations from `jobon_id`. The persistence graph stays minimal. The read model is composed for this one request.

---

## 4. JOB ON

### Access model

Job On uses module capabilities rather than legacy role titles:

- **Job On View** — navigate and consult Job On information.
- **Job On Create** — includes View and permits the Job On creation/editing actions available in the module.

The document therefore describes actions by module capability instead of legacy role titles.

Job On is the production occurrence. It is where a production is planned, where tools are selected, and where the production context is created.

### The flow

A user with **Job On Create** creates a Job On:

1. **Identify the production.** Reference, production number, machine, production date.
2. **Select the tools.** The user with **Job On Create** chooses the CM, MF, and BQ tools for this production. The system filters options by reference and machine. The user makes the final selection. The application does not infer or auto-select.
3. **Context snapshots are created.** For each selected tool, the system creates a new context ID (`cm_id`, `mf_id`, `bq_id`) that freezes the tool's current state. These snapshots belong to this Job On.
4. **Production-specific configuration.** PU, CS, TP and other production-specific values are configured manually through **Job On Create**. These belong to this production, not to the tool master.
5. **Verification occurrences.** Verification rules configured on the tool lots in Ferramentas generate occurrences for this production. They are explicitly confirmed during the production.

### Duplication

When a Job On is duplicated (a common pattern — most productions are similar to the previous one):

- A **new `jobon_id`** is created.
- The source contributes only the **`tool_id` identities** — which physical tools to use.
- **New context IDs** (`cm_id`, `mf_id`, `bq_id`) are created from the **current canonical Tool state** at the moment of duplication. The source's frozen snapshots are NOT copied verbatim. If the tool's lot changed between the source production and the duplication, the new production sees the updated lot.
- The source Job On and its contexts remain immutable.
- Production-specific configuration values are copied as a starting point, but a user with **Job On Create** reviews and adjusts them. They are not immutable defaults.

### The same tool across productions

The same physical tool (`tool_id`) can appear in many Job Ons. Each Job On creates its own context snapshot. The tool's master data in Ferramentas can change without affecting any past production's frozen context. This is how history stays truthful.

### What Job On does NOT do

Job On does not own tool master data. It does not store measurement results. It does not manage repair workflows. It creates the production context and provides it to downstream modules. Those modules consume the context; they do not modify it.

---

## 5. FERRAMENTAS

Ferramentas is the canonical tool registry. It answers: "What tools exist, and what are their current master facts?"

### tool_id as canonical physical identity

Each physical tool has one `tool_id`. Different lots of the same reference are different Tools (different `tool_id`). The registry stores: type (CM/MF/BQ), reference, lot, processo (NNPB/PS), machine/line compatibility, canonical quantity.

### Relationship with Job On contexts

Ferramentas provides the current canonical state. Job On reads it when creating context snapshots. After the snapshot is created, the snapshot is independent. If someone later edits the tool's master data in Ferramentas, no past production's context changes. The snapshot already froze what was true.

### What Ferramentas does NOT store

Ferramentas does not store production-specific values (like the Calote used in a specific production). It does not store measurement results. It does not store module-specific configuration. It stores the tool's identity and stable master facts. Production-specific and function-specific facts live in their respective operational records.

### Historical data is never rewritten

When the tool's lot is updated, or its processo is corrected, or a machine association changes, these changes affect the tool's **current** state. They do not propagate backward into existing `cm_id`/`mf_id`/`bq_id` snapshots. Each production remembers what it used.

---

## 6. CONTROLO

Controlo is an operational module containing several functions. It is not a single record type. It is a workspace where quality control happens.

### The functions inside Controlo

- **Resumo** — the entry point and dashboard for a production's control state.
- **Peso** — capacity and glass weight measurement per CM.
- **Comparação** — optional comparison during production, inside Peso.
- **Pegamentos** — dimensional overlap verification of CM + BQ + MF.
- **Folha** — consolidated per-piece evaluation (OK/NOK, observations).

### Peso is not Controlo

Peso is one function inside Controlo. Controlo also contains Comparação, Pegamentos, Folha, and Resumo. The navigation groups them together because they belong to the same operational module. But each function has its own records, its own lifecycle, and its own context anchors. They do not need a shared "Controlo aggregate" record merely because they appear in the same navigation menu.

### How the functions connect

Each function anchors to the production context through the component contexts created by Job On:

- Peso anchors to `cm_id` (it measures a specific CM).
- Comparação references `peso_id` and reuses `cm_id`.
- Pegamentos uses `cm_id`, `mf_id`, `bq_id` (it measures the CM + MF + BQ combination).
- Folha anchors to `jobon_id` (it evaluates the production as a whole).
- Resumo reads all of the above by traversal from `jobon_id`.

The frontend navigation groups these under "Controlo." The backend reads remain purpose-specific. Opening Peso does not load Pegamentos. Opening Resumo traverses to all of them. These are different read needs served by different queries against the same stored facts.

### Controlo Create vs Controlo Approve

Controlo has two operational surfaces:

- **Controlo Create** — where measurements are entered, records are created, and applicable settings are configured. Gated by the `controlo-create` capability.
- **Controlo Approve** — where records are reviewed, approved, rejected, and reopened. Gated by the `controlo-approve` capability.

Both surfaces share the same read model (`PesoSheetReadModel`). The Approve surface embeds the exact same sheet the Create surface renders, composed around approval-only presentation (decision controls, availability states). This parity is enforced by tests.

---

## 7. RESUMO

### The entry point

The Resumo page is the Controlo landing surface. Users enter **Controlo** through Resumo according to their granted Controlo capability. It answers: "What is the control state of this production?"

### How it works

The frontend sends `jobon_id` (or navigates through reference → production selection). The backend performs a focused traversal:

```
jobon_id
→ job_ons (production facts: reference, production number, machine, date)
→ cm_contexts / mf_contexts / bq_contexts (component contexts, tool identity)
→ pesos (Peso status, results — if any Peso exists)
→ comparacoes (Comparação state — if any comparison happened)
→ [pegamentos, folha — when implemented]
```

This is assembled at query time into a `ProductionResumoReadModel`. The current implementation resolves this with one context-specific statement: `job_ons LEFT JOIN cm_contexts LEFT JOIN tools`, keyed by `jobon_id`.

### What Resumo is NOT

Resumo is not the parent of Peso, Comparação, Pegamentos, or Folha. These records do not have a `resumo_id` FK. The dashboard displays them by traversing existing relations from `jobon_id`. The dashboard aggregates; it does not own.

### Navigation flow

The Resumo page always opens. With no query, it offers a reference lookup. A reference lookup lists that reference's Job On productions, newest first, for explicit selection (never auto-selected). A selected production renders the Resumo sheet anchored on its `jobon_id`. Switching the production replaces the entire production context.

---

## 8. PESO

### The operator flow

1. **Open Peso from the current production/CM context.** The operator is in Controlo Create. The production context is established (from Resumo, from Job On). The operator opens the Peso surface. The frontend knows `cm_id` (or `tool_id` if the Peso is pending association).

2. **Enter measurements.** The operator enters:
   - **Water temperature** (°C, range 5–35). This is the only temperature input. The operator does not enter water density.
   - **Per-row water weight** (g). Each row is one reading. Variable number of rows, at least one required.
   - **Volume Marisa/BQ** (cm³, optional). A drawing input.
   - **Volume Punção/PU** (cm³, optional). A drawing input.
   - **SAP references** (two optional text fields: previous production end, previous average weight). These are informational, not identities.

3. **Backend calculates.** All calculation is server-side. The frontend never computes results.
   - **Water density** is resolved from an authoritative 31-value table (one per whole degree, 5–35°C). The entered temperature is rounded to the nearest degree. The corresponding density is used. Never entered manually. Never interpolated.
   - **Glass density** is resolved from Controlo → Definições settings, keyed by processo (NNPB/PS), determined via `cm_id → tool_id → processo`. The resolved value is **frozen** on the Peso at first successful calculate/save. Later settings changes affect only new Pesos.
   - **Capacity** (per row) = water weight ÷ water density.
   - **Glass weight** (per row) = (Capacity + Volume Marisa/BQ − Volume Punção/PU) × glass density.
   - If any computed result is not strictly positive → typed refusal `RESULT_NON_POSITIVE` before any write.

4. **Save.** The Peso record is created or updated. `peso_id` is allocated by the backend. The record anchors to `cm_id` (production-bound) or `tool_id` (pending, "Job On por associar"). Status: `pendente`.

5. **Submit.** A user with **Controlo Create** explicitly submits the Peso. `submitted_at` and `submitted_by_user_id` are recorded. Status remains `pendente`. The Peso is now ready for review.

6. **Controlo Approve reviews and decides.** On the Controlo Approve surface, the user sees the exact same sheet (shared read model, renderer parity). They can:
   - **Approve** → status `aprovado`. Decision recorded in `peso_review_decisions` (append-only).
   - **Reject** → status `nao_aprovado`. Non-blank reason required. Decision recorded.
   - **Reopen** → status returns to `pendente`. Submitted facts cleared. Decision recorded. The Peso becomes editable/submittable again.
   
   The same `peso_id` persists through the entire lifecycle. No approval copy is created.

7. **Historical record remains frozen.** Once decided, the Peso's measurements, computed results, average, frozen density, and submitted facts are immutable. Comparação does not alter them. Later operations do not alter them.

### Individual results and average

Each row's capacity and glass weight are first-class individual results. The average (Média em água, Capacidade média, Peso médio do vidro) is supplementary information. It never replaces or hides individual values. Deviations (Desvio cm³, Desvio %) are computed against the sheet's own average.

### Tambão/Calote

Tampão/calote is an informational value: the spherical-cap volume of the container bottom, calculated as `π × s² × (3r − s) / 3` from sagitta and radius. It is NOT part of the main glass weight formula. It is distinct from Punção/PU (which IS in the formula). It is distinct from TP/Tampão in Job On (a production tooling configuration item). It is not currently implemented in the Peso calculation workflow. If presented, it is consultation-only.

### PDF and documents
The Peso PDF is generated in Controlo Create from the shared read model. It is stored in the production workspace: `<base>/<reference>/<production-number>/Peso_<reference>_<machine>.pdf`. The "Data" field in the PDF is the Job On production date, not the submission date. The PDF is a derived artifact; the structured record is the source of truth. The PDF is never silently overwritten.

---

## 9. COMPARAÇÃO

### What it is

Comparação is an optional workflow inside Peso that occurs during production. It allows re-measuring one or more CMs after the initial Peso has been decided, to verify consistency or investigate deviations. It is complementary to the initial control; it does not replace it.

### The flow

1. **Start a comparison event.** Against a decided Peso. A new `comparacao_id` is allocated. It references `peso_id`.

2. **Add CM subjects.** One or several CMs can be added. Each subject reuses the **existing `cm_id`** from the production context. No new CM identity is created. The subject identity is the natural composite key `(comparacao_id, cm_id)`.

3. **Record measurements.** Comparison measurements use the **same Peso calculation path** (`PesoRowCalculationRules`) with the initial Peso's frozen facts (water temperature, frozen glass density, volumes). The comparison never invents conditions. Measurement rows are stored in `comparacao_measurement_rows`, physically separate from `peso_measurement_rows`.

4. **Decide per CM.** Each measured CM subject receives an individual, explicit decision:
   - **Manter** (maintained/accepted). No justification required.
   - **Colocar de parte** (put aside). Justification required (non-blank reason).
   
   The decision is final within one comparison event. A second decision on the same subject is refused.

5. **Confirm.** The comparison event can only be confirmed when every measured CM subject has its final individual decision. Confirmation records `confirmed_at` and `confirmed_by_user_id`.

### What Comparação does NOT do

- It does NOT modify the original Peso. The Peso's measurements, results, average, status, approval, PDF, and frozen facts remain unchanged. Every Comparação write lives in the comparison tables only.
- It does NOT create a `previous_peso_id` relation. There is no comparison between Pesos. The comparison references one `peso_id` and reuses existing `cm_id` values.
- It does NOT create new CM identities.
- It does NOT require all CMs to be compared. One, two, four — any number is valid.

### Multiple events

Multiple comparison events can exist for the same Peso. Multiple events can reference the same `cm_id`. Each event gets its own `comparacao_id`. The history accumulates naturally.

### Implementation status

Backend, domain, persistence, service, repository, validator, and tests are fully implemented (migration 010). **User-facing UI is not yet implemented.** The functional workflow is defined; the presentation layer is pending.

---

## 10. PEGAMENTOS

### What it is

Pegamentos is the dimensional overlap verification of the CM + BQ + MF combination used in a Job On. It checks how the three tooling components align. It measures in two perpendicular axes.

### The measurement model

- **Costura** = 0° axis.
- **Contra costura** = 90° axis.
- **Ovalização** = Costura − Contra costura. The sign is preserved (functionally relevant).
- **Média** = (Costura + Contra costura) / 2.

### Nominal and tolerance

Each component (CM, BQ, MF) has its own nominal, inherited from the canonical Tool state. The tolerance corridor is **Nominal − 0.20 to Nominal + 0.20**. Boundary rules: reaching the limit → alert; crossing the limit → alert; equality at the limit → alert. Alerts never block, approve, or reject. They inform.

### Single-axis handling

A measurement is never blocked because one axis is absent. When Contra costura is not measured: Ovalização is undefined (not calculated); Média = Costura; the tolerance corridor applies to Média. An unmeasurable configuration yields `NotEvaluable`; a nominal is never invented.

### Context and tools

Pegamentos requires a Job On production context. It consumes the existing `cm_id`, `mf_id`, `bq_id` contexts. Tools are inherited from the Job On context; the operator never selects them. All calculation is server-side.

### May legitimately be absent

A production may have no Pegamentos record. Absence is a valid state, not an error.

### Implementation status

Pegamentos is **not yet implemented** in the current codebase. No migration, entity, service, endpoint, page, or test exists. The functional rules are established. The persistence shape must be derived from the minimum durable facts and the existing relation graph when implementation begins. **Do not assume a `pegamentos_id` or a specific table structure is required.** That is an implementation derivation, not a foregone decision.

---

## 11. FOLHA

### What it is

The Folha de Controlo is the consolidated per-piece evaluation layer. It covers five piece families: **CM, BQ, MF, PU, CS**. For each piece, it records:

- **OK/NOK** — the technical result.
- **Observation** — free-text comment.
- **MCaliper link** — where applicable (can be added, updated, or opened by the user; not automatically imported).

### Lifecycle

The Folha has its own lifecycle, distinct from Peso:

- **Rascunho** (draft) — **Controlo Create** edits and prepares.
- **Submetida** (submitted) — explicit action through **Controlo Create**. Not automatic.
- **Aprovada / Rejeitada** (approved / rejected) — the decision is made through **Controlo Approve**.
- **Reopen** — returns to Rascunho. Previous events are not erased.

Editing and technical evaluation belong to **Controlo Create**; the final decision belongs to **Controlo Approve**. A technical NOK does not automatically stop production. A technical OK does not automatically authorize it.

### PU and CS origin

PU and CS come from the exact Job On production/revision context — not from Armazém. Controlo consumes them but does not own their production configuration.

### Distinction from Resumo and Peso

The Folha is a distinct record from the Resumo (which is a summary/dashboard) and from Peso (which is a measurement record). The Folha evaluates; Peso measures; Resumo summarizes. They are connected through the production context, not through parent-child FKs.

### Implementation status

The persisted Folha evaluation layer is **not yet implemented**. The read projection for Resumo exists. The Folha record (per-piece OK/NOK, observations, MCaliper links, states, decision trail) has no table, no service, no UI. The functional rules are established. The persistence shape must be derived from the minimum durable facts when implementation begins.

---

## 12. BOQUILHAS

### What it is

Boquilhas registers the movements related to BQ external repair. It is a manual operational register: the operator introduces movements; the application stores them; the movement remains in history; the application presents the resulting outstanding balance.

### The flow

1. **Pre-JobOn: provisional anchor.** Boquilhas may start work before a Job On/BQ context exists. The register anchors provisionally to the canonical `tool_id`. This is a transitional state, not a permanent standalone flow.

2. **Association.** When a `bq_contexts` row arrives whose `tool_id` matches the register's provisional anchor, the register can be associated to that `bq_id` through explicit human confirmation. After association: the **same `boquilhas_id`** continues; `bq_id` is set; the provisional `tool_id` is cleared. The register is now production-linked: `boquilhas_id → bq_id → jobon_id + tool_id`.

3. **Movements.** Three movement types, exactly:
   - **`saida`** — sends BQ to external repair. Requires machine and repairer.
   - **`entrada`** — returns BQ from repair.
   - **`entrada_sem_reparacao`** — returns BQ that was NOT repaired. Does not mark the tool irreparable. Does not destroy it. Subtracts from outstanding exactly like `entrada`.

4. **Outstanding derived at read time.** `outstanding = Σ(saida) − Σ(entrada) − Σ(entrada_sem_reparacao)`. Never stored. Negative values are valid and visible. No blocking.

5. **Editing.** Editing a movement mutates the same `movement_id`. Writes one before/after audit row in the same transaction. `recorded_at` is immutable. `movement_type` is immutable. `business_date` is editable.

### Repairers and Definições

The repairer register and machine→repairer assignments belong to **Boquilhas → Definições**. Six independent machines: B1, B2, B3, C1, C2, C3. No grouping. "Sem associação" is allowed. Changing an assignment does not rewrite historical movements. The selected repairer is stored on the movement.

### What Boquilhas does NOT have

No lifecycle (no open/closed, no close/reopen). No `Início` movement. No `Irreparável` movement. No four-bucket balance model. No permanent standalone flow.

---

## 13. DOCUMENTS

### Three distinct things

```
record ≠ PDF ≠ path
```

- **The record** is the persisted operational fact (Peso, Folha, Boquilhas movement). It is the source of truth.
- **The PDF** is a derived artifact generated from the record. It is printable and regenerable. It is not the source of truth. If the PDF and the record disagree, the record prevails.
- **The path** is where the PDF is stored in the filesystem. It is a location, not an identity. Filename and path are never used as join keys.

### Production directory organization

```
<base_directory>/
└── <reference>/
    └── <production_number>/
        ├── Peso_<reference>_<machine>.pdf
        ├── Resume_<reference>_<machine>.pdf
        └── Pegamentos_<reference>_<machine>.pdf
```

The base directory is operator-configured in Controlo → Definições. Reference precedes Production. Lower folders are created or reused automatically (idempotent). Job On accesses the same production/revision document relationship. No duplicate document tree owned by Job On.

### Document generation and sending

PDF generation occurs in Controlo Create. The PDF is generated from the shared read model. Preconditions: the record must be decided and production-bound. The "Data" field is the Job On production date. The PDF is never silently overwritten.

Email sending is manual, from Controlo Create. Routing uses machine-group resolution: B1/B2/B3 → Group B; C1/C2/C3 → Group C. The group resolves the template; the template resolves the recipient list. No manual list selection. No hardcoded recipients.

### Availability

Document availability uses the shared `AvailabilityState` vocabulary: `Available`, `NotGenerated`, `NotFound`, `WorkspaceUnavailable`, `Refused`. Absence of a generated document is not automatically an error.

---

## 14. HOT PATH VS HISTORY

### The daily path stays light

Most operations are simple. A user with **Controlo Create** opens Peso, enters measurements, and submits. A user with **Controlo Approve** opens the Approve surface, reviews, and decides. A user with access to **Controlo** opens Resumo and checks the production state.

These operations traverse only the relations they need:

- **Opening Peso:** `peso_id → cm_id` plus the Peso's own facts. One context hop. No tool history. No cross-production traversal.
- **Opening Resumo:** `jobon_id → contexts → function records`. A broader query, but still bounded to one production.
- **Submitting, approving, deciding:** Write to the specific record. No cascade to other records. No dashboard update. No aggregate recalculation.

### Deep history is explicit and exceptional

Sometimes someone asks: "Show me everything that happened to this tool across six productions." Or: "Compare the Peso results for this reference over the last year."

These queries traverse longer paths:

```
tool_id → cm_contexts → jobons → pesos → decisions
```

This is a heavier query. That is acceptable because it is exceptional, explicit, and user-initiated. The user asked for depth; the system provides depth.

### Why the persistence model is not denormalized for rare queries

Adding `jobon_id` directly to `pesos` would make one production-level query skip a join. But it would create a duplicate production anchor on every Peso record. It would burden every daily Peso write with an extra fact that is already reachable through `cm_id → jobon_id`. It would serve the exceptional query at the cost of the hot path.

The system prefers: **simple truthful writes + purpose-built filtered reads** over **complex pre-consolidated writes + generic giant reads.**

---

## 15. HOW NEW FEATURES SHOULD FIT

When adding a new feature, follow this reasoning sequence:

1. **What real operational event creates the fact?** Name the concrete moment: **Controlo Create** records a measurement, **Controlo Approve** records a decision, the system resolves a calculation. If you cannot name the moment, the fact may not be real yet.

2. **When does it become known?** At tool registration? At Job On creation? During measurement? At decision time? Only during historical review? The moment of discovery constrains where it can truthfully live.

3. **What context is it valid in?** Is it valid forever (stable tool property)? Valid for one production (production-specific)? Valid only inside one workflow (function-specific)? Derivable from other stored facts? Must it preserve exactly what was true at a specific moment (historical snapshot)?

4. **What existing relation already reaches that context?** Trace the existing relation graph. Does `cm_id → jobon_id → tool_id` already provide the context? Can the fact be reached by continuing an existing true relation?

5. **Is it a write need or only a read need?** If one screen needs to see several related facts, that is a READ need — use a focused Dapper query. Do not denormalize the write model. If a new fact comes into existence that has no truthful home, that is a WRITE need — determine where it should be stored.

6. **Does it genuinely need a new identity/table?** Is there an independent lifecycle? Genuine 1:N multiplicity? A concurrency boundary? Append-only events that must be individually addressed? Facts that otherwise have nowhere truthful to live? If none of these apply, do not create a new persistence object.

7. **What is the shortest truthful hot path?** For the daily operation, what is the minimum traversal? `peso_id → cm_id` plus the record's own facts? Do not force the hot path to carry history it does not need.

8. **What history actually needs preserving?** Does the existing context snapshot already handle it? Does the accumulation of durable records already create the history? A separate history mechanism is only needed when a specific fact cannot be reconstructed from existing records.

9. **Can Dapper compose the required screen without a new persisted shortcut?** If the frontend workflow already identifies the context and the backend can retrieve the data through a focused query using existing truthful relations, a new persisted relation is likely unnecessary.

---

## 16. WHAT NOT TO DO

- **Do not create an ID for every noun.** An identity exists because there is a meaningful durable entity with a lifecycle, multiplicity, or concurrency boundary. A concept having a name does not mean it needs a table.

- **Do not create direct FKs only for navigation convenience.** If `A → B → C` already expresses reality, do not add `A → C` because it makes a query or diagram look easier. Navigation convenience is not a domain relationship.

- **Do not mirror frontend navigation as database hierarchy.** Controlo contains Peso, Comparação, Pegamentos, Folha, and Resumo in the UI. This does not mean the database needs a `controlo_id` parent or a `resumo_id` container. The navigation menu and the persistence graph answer different questions.

- **Do not create giant backend aggregates "just in case."** The frontend workflow knows the context. The backend retrieves what the current operation needs. A `ControloAggregate` containing Job On + Tool + CM + Peso + Comparison + Pegamentos + Folha + full history is not how this system works.

- **Do not turn `tool_id` into a container for every historical or module-specific fact.** The tool identity answers "which physical tool?" It does not carry production-specific values, measurement results, or repair histories. Those live in their respective operational records, reachable through context relations.

- **Do not build generic history/context/metadata tables without a real need.** History emerges from the accumulation of durable operational records. A generic history table is only justified when a specific fact cannot be reconstructed from existing records.

- **Do not confuse read-model composition with persisted domain structure.** A Dapper query that joins five tables and projects a dashboard DTO does not mean those five tables need a shared parent. The query is a read composition. The persistence graph stays minimal.

---

## 17. ONE COMPLETE WALKTHROUGH

### The physical tool already exists

CM tool `T-5447` is registered in Ferramentas: `tool_id = 7a3f…`, type CM, reference `5447T173`, lot "3", processo NNPB, machines B1/B2/B3. It has been used in several previous productions.

### A new Job On is created

A user with **Job On Create** creates Job On `J-2026-078`: reference `5447T173`, production number `2026078`, machine `B2`, production date `2026-07-15`.

They select tools: CM `T-5447`, MF `T-5447-MF`, BQ `T-173-BQ`.

The system creates context snapshots:
- `cm_id = c4a1…` → `jobon_id = J-2026-078` + `tool_id = 7a3f…` (frozen: lot "3", reference "5447T173")
- `mf_id = m8b2…` → `jobon_id = J-2026-078` + `tool_id = 9c4e…`
- `bq_id = q2f7…` → `jobon_id = J-2026-078` + `tool_id = 3d1a…`

**Write:** `job_ons` row, three context rows. **Read:** Ferramentas tool registry (to filter and display tool options).

### The operator opens Controlo

A user with **Controlo Create** enters Controlo through Resumo. The frontend sends `jobon_id = J-2026-078`. The backend performs a focused query: `job_ons LEFT JOIN cm_contexts LEFT JOIN tools WHERE jobon_id = J-2026-078`. The Resumo page renders: production facts, component contexts, no Peso yet, no Comparação, no Pegamentos.

**Read:** One context-specific statement. No Peso data loaded (none exists). No Boquilhas data. No cross-production traversal.

### Peso is created and measured

A user with **Controlo Create** opens Peso for `cm_id = c4a1…`. The frontend sends `cm_id`. The backend validates: the `cm_id` exists, belongs to `J-2026-078`, and resolves to `tool_id = 7a3f…` (processo NNPB).

Through **Controlo Create**, the user enters:
- Water temperature: 24°C
- Per-row water weights: 412.3g, 415.1g, 411.8g
- Volume Marisa/BQ: 18.5 cm³
- Volume Punção/PU: 12.2 cm³

The backend resolves:
- Water density for 24°C: 0.99732 g/cm³ (from the authoritative table)
- Glass density for NNPB: 2.4027 g/cm³ (from Definições settings, frozen on this Peso)
- Per-row capacity: 412.3 ÷ 0.99732 = 413.41 cm³, etc.
- Per-row glass weight: (413.41 + 18.5 − 12.2) × 2.4027 = 1008.52g, etc.

**Write:** `pesos` row (`peso_id = p5c9…`, `cm_id = c4a1…`, status `pendente`, frozen glass density 2.4027), `peso_measurement_rows` (three rows). **Read:** water density table, glass density settings, CM context.

A user with **Controlo Create** submits. `submitted_at` and `submitted_by_user_id` are recorded. Status remains `pendente`.

**Write:** Update `pesos` row (submitted facts).

### Controlo Approve decides

A user with **Controlo Approve** opens the approval surface. The frontend sends `peso_id = p5c9…`. The backend loads the shared `PesoSheetReadModel` — the exact same read model the Create surface uses. The user sees the sheet, reviews the values, and approves.

**Write:** Update `pesos` row (status `aprovado`). Insert `peso_review_decisions` row (append-only: decision `aprovado`, decided_by, decided_at, version, prior_status `pendente`). **Read:** `pesos`, `peso_measurement_rows`, `cm_contexts`, `tools`.

The Peso is now frozen. Its measurements, results, average, density, and submitted facts are immutable.

### Later, Comparação happens

During production, the operator notices a deviation. They start a Comparação against `peso_id = p5c9…`.

**Write:** `comparacoes` row (`comparacao_id = k8d3…`, `peso_id = p5c9…`).

They add one CM subject: `cm_id = c4a1…` (reused, not created).

**Write:** `comparacao_cm_subjects` row (`comparacao_id = k8d3…`, `cm_id = c4a1…`).
They record comparison measurements using the Peso's frozen facts.

**Write:** `comparacao_measurement_rows` rows.

They decide: `Manter` for this CM.

**Write:** Update `comparacao_cm_subjects` row (decision `Manter`, decided_by, decided_at).

They confirm the comparison event.

**Write:** Update `comparacoes` row (`confirmed_at`, `confirmed_by_user_id`).

**The original Peso is unchanged.** `pesos` row `p5c9…` was not written. `peso_measurement_rows` were not written. The average was not recalculated. The approval was not modified. The PDF was not regenerated.

### Pegamentos may exist

If the operator performed a Pegamentos check during this production, a Pegamentos record would exist anchored to the component contexts (`cm_id`, `mf_id`, `bq_id`). It would record Costura, Contra costura, computed Ovalização and Média, nominal from Tool, tolerance alerts. In this walkthrough, Pegamentos was not performed. Its absence is valid.

**No write.** No record. The production simply has no Pegamentos data.

### Folha evaluates the production

A user with **Controlo Create** opens the Folha for `jobon_id = J-2026-078` and evaluates each piece:
- CM: OK
- BQ: OK, observation "minor wear noted"
- MF: OK
- PU: NOK, observation "surface defect", MCaliper link added
- CS: OK

They submit the Folha. A user with **Controlo Approve** reviews and approves.

**Write:** Folha record (per-piece evaluations, observations, MCaliper links, state transitions, decision trail). **Read:** Job On context, component contexts, Peso results (for reference).

*Note: The Folha persistence layer is not yet implemented. This describes the intended flow.*

### Resumo displays the overall state

A user with access to **Controlo** opens Resumo for `J-2026-078`. The backend traverses:

```
jobon_id = J-2026-078
→ job_ons (reference, production, machine, date)
→ cm_contexts, mf_contexts, bq_contexts (component contexts)
→ pesos: p5c9… status aprovado
→ comparacoes: k8d3… status confirmed
→ [pegamentos: absent]
→ [folha: approved]
```

The dashboard renders: production context, Peso approved, one Comparação confirmed, no Pegamentos, Folha approved. All assembled at query time from existing stored facts. No `resumo_id` FK on any function record. No pre-built aggregate.

**Read:** One or two focused traversal queries. No writes.

### Next Job On reuses the same Tool

Two weeks later, a user with **Job On Create** creates Job On `J-2026-091` using the same CM tool `T-5447` (`tool_id = 7a3f…`).

The system creates a **new** `cm_id = c7e5…` → `jobon_id = J-2026-091` + `tool_id = 7a3f…`. Between the two productions, the tool's lot was updated to "4" in Ferramentas. The new snapshot freezes lot "4".

**Production A (`J-2026-078`) still shows lot "3." Production B (`J-2026-091`) shows lot "4."** The tool's master data changed; the historical productions did not.

### Two years later: inspecting the Tool history

Someone asks: "Show me every production this CM tool was used in."

The backend performs a deep traversal:

```
tool_id = 7a3f…
→ cm_contexts WHERE tool_id = 7a3f…
→ jobons (each cm_id's jobon_id)
→ pesos (each cm_id's Peso, if any)
→ comparacoes (each peso_id's comparisons, if any)
→ decisions
```

This is a longer query. It traverses many productions. It is heavier than the daily path. That is acceptable because it is exceptional and explicit. The user asked for the tool's complete history.

Every production's data is intact. Every Peso's measurements, decisions, and frozen densities are preserved. Every Comparação's subjects, measurements, and decisions are preserved. The tool's current master state in Ferramentas reflects today's truth. The historical productions reflect their own truth. Neither overwrites the other.

The system did not need a `tool_history` table. It did not need a `production_id`. It did not need a generic event log. The history exists because the durable operational records exist, connected by truthful relations, frozen at the boundaries where history matters.

---

*Persist reality. Compose context at query time. Keep the hot path light. Preserve history where history matters. Do not invent what the flow does not require.*