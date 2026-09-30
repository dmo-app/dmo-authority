# Beta Scope Boundaries

**Definition:** The Beta is a **scope-reduced** DMO product. Capabilities listed as OUT remain outside this Beta unless authority is explicitly changed.

## IN SCOPE

1. **Identity & Access** — Admin, Users, Templates.
2. **Ferramentas / Tools** — canonical `tool_id` registry for CM, MF and BQ; consultation/filtering is the primary surface and creation is an action inside the registry. Selection is explicit and Ferramentas remains contextual to consuming workflows.
3. **Job On (Light)** — production planning and production-context creation: `jobon_id`, `cm_id`, `mf_id`, `bq_id`.
4. **Controlo**
   - **Resumo** — tab/function inside Controlo; the consolidated output is a product of Controlo, not the production-level node itself.
   - **Peso** — measurement, calculation and submission.
   - **Comparação** — optional child workflow of an existing Peso.
   - **Pegamentos** — in Beta scope; backend still not implemented.
   - **Approve** — review and explicit human decision over submitted Peso records.
5. **Boquilhas** — BQ repair traces grouped by `bq_repair_trace_id`, with pre-JobOn anchoring to `tool_id` where applicable, later association to `bq_id`, three movement kinds, derived outstanding quantity/discrepancy and module-local history.

## OUT OF SCOPE

- **Armazém** — stock, locations and logistics.
- **Reparação Interna / Reparação Programada** — workshop flows.
- **Tampões as an independent module.**
- **Full Tool lifecycle** — verification/reset/deactivation workflows.
- **HISTÓRICO GLOBAL** as a top-level aggregating destination. Module-local Histórico remains in scope where its owning module requires it.
