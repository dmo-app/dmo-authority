# Controlo

Controlo is one operational module with two access capabilities:

```text
Controlo
├── Create
└── Approve
```

Create and Approve are not separate product modules. They are different permissions/workflows over the same Controlo domain and production context.

This file is the single functional source for Controlo.

## The approval rule

Only **Peso** has an approval lifecycle.

```text
Controlo Create
├── Peso → create / measure / calculate / save / submit
├── Pegamentos → measure / record
├── Folha → fill / evaluate / save
├── Resumo → read/composition
└── Comparação → operational verification after an approved Peso

Controlo Approve
└── Peso → review / approve / not approve / reopen where allowed
```

There is no approval lifecycle for:

- Pegamentos;
- Folha;
- Resumo;
- Comparação as a second approval of Peso;
- a generic Controlo object.

The chief/approver approves the submitted Peso. That Peso is the approved result used for production.

## Production and Controlo context

Controlo works in the context of a Job On production.

Job On provides:

- `jobon_id`;
- `cm_id`;
- `mf_id`;
- `bq_id`;
- production facts owned by Job On.

Each successfully created Job On also creates and associates one canonical Controlo context:

```text
jobon_id
↔ controlo_id
```

`controlo_id` identifies the durable shared Controlo context for that production. It exists before Peso, Folha, Pegamentos or another Controlo record is created.

`controlo_id` does not replace:

- `jobon_id`;
- `cm_id`, `mf_id`, `bq_id`;
- `peso_id`;
- `comparacao_id`;
- other real function-specific identities where required.

It must not become a generic parent merely because several screens appear under Controlo or because a query would be shorter.

## Pre-production control

Where the owning workflow supports it, a control record may exist before Job On.

The application must not fabricate `jobon_id`, `cm_id`, `mf_id` or `bq_id` merely to make a pre-production record look production-bound.

Peso explicitly supports this behavior: before Job On it may be anchored directly to the canonical CM `tool_id`; when that Tool is selected in Job On, the same `peso_id` associates to the resulting `cm_id` and the temporary direct `tool_id` anchor is cleared.

## Future-production preparation

A saved future Job On is immediately available to Controlo as a preparation context.

```text
future Job On saved
→ associated controlo_id already exists
→ production appears as a selectable Controlo/Resumo context
→ user explicitly selects it
→ preparation may begin where the workflow allows
```

This is explicit planning/preparation selection. It does not mean that the machine or another module has already transitioned operationally to that future production.

## Resumo

Resumo is a shared read/composition surface over the real production/control records.

There is no canonical `resumo_id`.

Resumo is not:

- a persisted parent;
- a foreign-key anchor;
- a duplicated Create/Approve object;
- the owner of Peso, Comparação, Pegamentos or Folha.

Create and Approve may read the same Resumo composition according to their permissions. Approve does not gain edit ownership over Create-owned operational facts because it can see them.

The operator may explicitly navigate among saved production/Controlo contexts. If several Job Ons are candidates, the application must show them and let the user choose; it must not silently activate one.

## Peso

`peso_id` is the durable identity of one Peso.

The same `peso_id` persists through:

```text
create
→ save
→ calculate
→ submit
→ approval / not approval
→ reopen where allowed
→ correction
→ resubmit
→ later approval
→ history
```

Approval never creates a second Peso.

### Save vs submit

Save and submit are different operations.

```text
Save
→ persists current work
→ remains editable in Create
→ does not enter Approve

Submit
→ status = por_aprovar
→ becomes available to Approve
→ normal Create editing is blocked while awaiting decision
```

A saved Peso that has never been submitted does not require a separate `draft` status.

Current approval statuses are:

```text
por_aprovar
aprovado
nao_aprovado
```

### Pre-production Peso association

Before production context exists:

```text
peso_id → CM tool_id
```

When that exact CM Tool is selected in Job On:

```text
peso_id → cm_id → CM tool_id
peso direct tool_id → cleared
```

The Job On Tool selection is already the association decision. There is no second “associate Peso?” prompt.

A single Peso may contain several physical measurement rows. Those rows do not create additional canonical `cm_id` or `tool_id` identities. Visible CM number/position inside the Peso is measurement-row data, not a production-context identity.

### Physical measurement and calculation

Physical weighing uses:

```text
CM + TP
```

TP/Calote is physically present in the measurement and its Job On production value may be used to interpret/correct the observed measurement for the relevant analysis.

TP is not a term in the main Peso formula.

The main technical calculation is:

```text
capacity = water weight / water density

glass weight =
(capacity + volume_marisa - volume_puncao) * glass density
```

The frontend does not independently calculate the industrial result.

Water density:

- comes from the canonical table;
- valid temperature range: 5–35 °C;
- rounded whole-degree temperature is used;
- no interpolation.

Glass density:

- configured by process where applicable;
- frozen when consumed by the Peso so historical calculation remains reproducible.

### Tool-owned inputs consumed by Peso

```text
volume_puncao
peso_id → cm_id → CM tool_id → tool technical values

volume_marisa
peso_id → jobon_id → bq_id → BQ tool_id → tool technical values

peso_nominal
peso_id → cm_id → CM tool_id → tool technical values

process
peso_id → cm_id → CM tool_id → Tool.process
```

Supported CM processes are `NNPB` and `PS`.

If a required Tool value is missing, Peso tells the user which owner value is missing; the user completes/corrects it in Ferramentas; Peso re-reads the Tool. Peso must not invent the value or become a second editable owner.

Where historical reproducibility requires it, the exact consumed input may be frozen with the Peso as historical evidence.

## Peso historical difference — regression-critical rule

The normal Peso workflow may compare the current Peso against a historical Peso for the same production reference.

This is normal Peso behavior. It is **not** the separate Comparação workflow and does not create `comparacao_id`.

The user explicitly chooses the historical `peso_id`.

Candidate presentation may use same-machine, relevant/compatible Tool context and recency as ranking assistance only. Different machine or Tool context must not remove an otherwise valid historical Peso from the candidate set.

The application must never silently choose the first/latest/only candidate.

### Unequal measurement counts are valid

The current and historical Peso do **not** need the same number of measurement rows.

Examples:

```text
current = 4 measurements
historical = 5 → valid
historical = 6 → valid
historical = 3 → valid
```

Only rows with a valid measurement counterpart participate.

```text
current:    CM1 CM2 CM3 CM4
historical: CM1 CM2 CM3 CM4 CM5

CM1 ↔ CM1
CM2 ↔ CM2
CM3 ↔ CM3
CM4 ↔ CM4
CM5 → unmatched → excluded
```

Matching is measurement-row correspondence, not equality of production `cm_id` between different productions.

Unmatched rows are excluded. They must not be fabricated and must not block the Peso.

The historical difference is refused only when **no valid measurement-row counterpart exists**.

A same-count validation that blocks or destroys access to historical Peso data is forbidden.

The detailed implementation regression contract is preserved in `backend/PESO_HISTORICAL_DIFFERENCE.md`.

## Comparação

Comparação belongs to Controlo Create and is a separate operational verification event after an approved Peso.

It may start only from:

```text
peso.status = aprovado
```

Starting a Comparação creates a new `comparacao_id`.

It references the existing approved `peso_id` and reuses that Peso's production `cm_id`. It does not create another CM production-context identity.

Flow:

1. start from approved `peso_id`;
2. create new `comparacao_id`;
3. record the required confirmation measurement(s);
4. explicitly decide `Manter` or `Colocar de parte`;
5. require a non-empty justification for `Colocar de parte`;
6. confirm the event.

Warnings/calculations may inform the person but must not silently choose the decision.

Comparação does not:

- approve the Peso again;
- change the original Peso approval status;
- rewrite original Peso measurements/results;
- create `previous_peso_id`;
- compare two Peso records as its identity model.

Multiple Comparação events may exist for the same approved Peso. Once confirmed, an event is historical; a later issue creates another `comparacao_id` instead of reopening/overwriting the previous event.

## Pegamentos

Pegamentos belongs to Controlo Create and has no approval workflow. It may legitimately be absent for a production.

It uses the existing production component contexts:

```text
cm_id
mf_id
bq_id
```

Pegamentos does not reselect Tools.

Tool throat-diameter nominal values are resolved from the actual Tool owner:

```text
cm_id → CM tool_id → diametro_gargalo
bq_id → BQ tool_id → diametro_gargalo
mf_id → MF tool_id → diametro_gargalo
```

If a required diameter is missing, the user completes it in Ferramentas and Pegamentos re-reads the Tool.

### Measurement rules

- Costura = 0° axis.
- Contra costura = 90° axis.
- Ovalização = Costura - Contra costura.
- Média = (Costura + Contra costura) / 2 when both axes are measurable.
- Default tolerance = nominal ± 0.20 mm unless configured otherwise.
- dimensional values use millimetres and two decimal places.

Tolerance is evaluated per measurement row. Reaching or exceeding either boundary raises a non-blocking alert.

A valid overall/component average must not cancel an alert from an individual row.

If CM geometry does not allow a valid 90° measurement:

```text
Costura → recorded
Contra costura → not applicable
Ovalização → not calculated
Média = Costura
```

A fake second-axis value must not be required.

Tolerance alerts are informational. They do not approve/reject or stop production automatically.

### Pegamentos persistence boundary still requiring implementation closure

The current functional measurement behavior is known, but implementation must not invent missing persistence semantics. The durable identity/anchor, cardinality/history, edit lifecycle, stable row addressing, configurable-tolerance ownership and component applicability must be explicitly closed before persistence implementation where they are not already determined by the final schema/design.

Do not invent `pegamentos_id` merely for symmetry.

## Folha

Folha belongs to Controlo Create.

**Folha has no approval lifecycle.**

There is no Folha:

- submit-for-approval lifecycle;
- approve;
- reject;
- reopen;
- approval copy.

Create may read/edit/save/evaluate the operational Folha facts.

Folha may contain control/evaluation information for:

- CM;
- BQ;
- MF;
- PU;
- CS.

CM/BQ/MF use their real production contexts.

PU and CS are fixed Folha fields, not canonical Tools or dynamic identities. Do not create `pu_id`, `cs_id`, PU/CS Tool selection or dynamic identity mapping for them.

Applicable fields may carry OK/NOK and observation/content.

OK/NOK are control/evaluation facts. NOK does not automatically stop production and OK does not automatically authorize production.

MCaliper is outside the current scope and must not be required for Folha or Resumo.

## Controlo Approve

Approve is the review/decision capability for submitted Peso.

Approve may:

- review a submitted Peso;
- read the production/Resumo context needed to understand it;
- approve;
- not approve with required reason/context where applicable;
- reopen according to the Peso workflow;
- expose decision history.

Approve writes approval-side facts such as decision, reason/context, actor, timestamp and history.

Approve does not edit Create-owned operational measurements/Folha/Pegamentos merely because it can read them.

A decision is applied to the same durable `peso_id`; no approval copy is created.

## Configuration

Controlo settings are owned by Controlo but edited through:

```text
Admin
→ App Definições
→ Controlo
```

There is no requirement for a daily operational “Definições” tab inside Controlo Create.

Current settings include at least:

- base directory for generated PDFs;
- glass density by process where applicable;
- email templates;
- production recipient/person configuration;
- B-line recipients;
- C-line recipients;
- fast sending of Peso/PDF artifacts.

Current recipient routing:

```text
B1/B2/B3 → B recipient configuration
C1/C2/C3 → C recipient configuration
```

Recipient addresses/templates must not be hardcoded in the sending workflow.

Production/email recipients are not automatically DMO authentication users.

## Documents / PDFs

Persisted operational records remain the source of truth.

```text
record ≠ PDF ≠ filesystem path
```

PDFs are derived artifacts for presentation/printing/distribution. Paths are storage locations, not domain identities or join keys.

Peso production PDF is generated from an approved Peso. After reopen/correction/resubmission/new approval, the same `peso_id` may explicitly regenerate/replace its production PDF from the newly approved state.

Silent overwrite is forbidden.

Sending is an explicit user action and uses configured routing.

Detailed document/Peso PDF field-source and storage rules live in `backend/DOCUMENTS.md`.

## Rules that must not be violated

An implementation must not:

- model Create and Approve as separate product domains;
- add approval lifecycle to Pegamentos, Folha, Resumo or Comparação;
- approve a generic Controlo object instead of the submitted Peso;
- create an approval copy of Peso;
- create `resumo_id`;
- turn `controlo_id` into a replacement for Job On/component/function identities;
- make every Controlo record a child of `controlo_id` merely for navigation convenience;
- fabricate production IDs for valid pre-production records;
- automatically choose a future Job On, historical Peso or Tool;
- require equal Peso measurement counts for historical difference;
- delete/block historical Peso truth because measurement counts differ;
- confuse normal Peso historical difference with Comparação;
- start Comparação from a non-approved Peso;
- let Comparação change the original Peso approval state;
- create new CM identities for physical measurement rows;
- let frontend own industrial formulas;
- let Pegamentos tolerance alerts become approval/production-stop decisions;
- invent Pegamentos persistence identities/lifecycle where not yet closed;
- create Folha approve/reject/reopen behavior;
- create PU/CS identities;
- use PDF/path as persistence truth.
