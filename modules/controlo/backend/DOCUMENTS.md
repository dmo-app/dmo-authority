# Controlo — Documents / Peso PDF Backend Contract

Generated documents are derived artifacts. They are not persistence authorities.

```text
record ≠ PDF ≠ filesystem path
```

The persisted operational record remains the source of truth.

## Storage

The configured base directory is a storage location, not a domain identity or join key.

Current production document tree:

```text
<base_directory>/
└── <reference>/
    └── <production_number>/
        ├── Peso_<reference>_<machine>.pdf
        ├── Resume_<reference>_<machine>.pdf
        └── Pegamentos_<reference>_<machine>.pdf
```

Required directories are created/reused. Re-entering the same production path must not create a parallel duplicate tree.

Generated PDFs must not be silently overwritten. Replacement must be explicit.

## Sending

Sending is an explicit/manual action from the owning Controlo workflow.

Recipient routing is configuration-driven:

```text
B1/B2/B3 → B recipient configuration
C1/C2/C3 → C recipient configuration
```

Email addresses/templates must not be hardcoded in the send workflow. Production/email recipients are not automatically DMO authentication users.

## Availability

Document absence is not automatically an application error.

Useful availability states include:

- `Available`;
- `NotGenerated`;
- `NotFound`;
- `WorkspaceUnavailable`;
- `Refused`.

These describe the document artifact, not the underlying operational record state.

## Peso PDF

The Peso PDF is the final production document derived from one approved `peso_id`.

```text
approved peso_id
→ backend resolves truthful source relations
→ backend builds purpose-specific PDF read model
→ renderer formats that packet
```

The renderer must not reconstruct domain truth independently.

### Production block

| Field | Source |
| --- | --- |
| Referência | Job On production reference |
| Produção | Job On production number |
| Linha | Job On machine/line |
| Data | Job On production start date |

These values are resolved through the persisted Peso/Controlo/Job On relations and are not duplicated merely for rendering.

### CM block

| Field | Source |
| --- | --- |
| Referência CM | selected CM Tool/context |
| Lote | canonical CM Tool / preserved production context where required |
| Estado | selected CM Tool/context state |

Visible lot uses the raw lot value. A filename formatting prefix must not become part of the domain lot value.

### Technical references

| Field | Source |
| --- | --- |
| Processo | selected CM Tool process (`NNPB` / `PS`) |
| Volume BQ | BQ Tool `volume_marisa` |
| Volume PU | CM Tool `volume_puncao` |
| Volume TP | production-specific Job On TP/Calote value |

TP appearing in the PDF does not make it a term in the main Peso formula.

Missing owner values must remain missing/refused according to the owning workflow; the PDF must never invent them.

### Calculated Peso values

The backend supplies the industrial results.

```text
capacity = water weight / water density

glass weight =
(capacity + volume_marisa - volume_puncao) * glass density
```

The PDF compares/displays calculated glass weight and water volume/capacity. The renderer does not calculate them independently.

### Historical-control comparison

The comparison uses the historical `peso_id` explicitly selected by the user in the normal Peso workflow.

It must never silently switch to another historical Peso because a newer/closer candidate exists.

The PDF identifies enough production context to show which historical control was selected, such as production number, date and machine/line.

Detailed by-CM comparison uses only valid measurement counterparts from `PESO_HISTORICAL_DIFFERENCE.md`. Unequal row counts remain valid.

### Technical/reference section

The selected historical DMO Peso and external/reference information are distinct sources.

For example:

```text
selected historical Peso
!=
SAP/reference data for previous production
```

Relevant fields may include:

- nominal drawing weight from the CM Tool technical values;
- backend-derived difference/variation;
- previous-production SAP/reference value;
- SAP period;
- consumed/frozen water temperature;
- consumed/frozen water density.

External/reference data must come through an owning backend contract, not be scraped from display text or guessed by the renderer.

### Traceability

Traceability comes from persisted backend attribution/history, not from the person who clicks “generate PDF”.

The read model may expose:

- submit/verification actor where recorded;
- approval actor;
- approval timestamp;
- document revision metadata.

Generating the PDF must not replace those historical actors with the current generator.

### Backend read model

The Peso PDF read should be purpose-specific:

```text
read by peso_id
→ validate approved state
→ resolve Job On / Controlo / CM context
→ resolve required Tool facts and technical values
→ resolve frozen Peso inputs/results
→ resolve explicitly selected historical Peso
→ resolve required drawing/SAP reference data
→ resolve approval/audit facts
→ return compact Peso PDF read model
```

A read-model join does not create new write-model ownership.

### Renderer responsibilities

The document/frontend layer may own:

- layout;
- labels;
- number/unit formatting;
- pagination/printing;
- explicit generation/send actions.

It must not:

- derive Tool identity from labels;
- reconstruct the packet from arbitrary tables;
- recalculate industrial formulas as another authority;
- decide which historical Peso was intended;
- invent approval actor/time;
- reinterpret external reference data as the selected historical DMO control.

## Regeneration

The same `peso_id` survives reopen/correction/resubmission/reapproval.

After a corrected Peso is approved again, its production PDF may be explicitly regenerated/replaced from that newly approved state.

This does not create a new Peso identity and the old PDF never becomes the source of truth.

## Boquilhas boundary

Boquilhas does not require a PDF/email artifact in the current scope. This Controlo document contract must not be used to invent one.
