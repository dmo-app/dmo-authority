# Ferramentas

Ferramentas is the canonical registry of physical Tools used by DMO.

This file is the single functional source for the Ferramentas module. Backend/frontend material may add technical detail, but must not redefine these rules.

## Canonical identity

```text
tool_id
= one specific registered Tool / lot
```

A Tool belongs to one Tool type:

- CM;
- MF;
- BQ.

Reference, lot, type, machine compatibility and other visible facts help humans find and distinguish Tools. They do not replace `tool_id` as canonical identity.

A different lot is a different real Tool and therefore receives a different `tool_id`.

The application must never derive or reconstruct `tool_id` from reference + lot + machine or similar visible fields.

## Core Tool data

A Tool may contain the operational master data required to identify and use it, including:

- Tool type;
- canonical reference;
- lot;
- quantity where applicable;
- machine/line compatibility or association where applicable;
- manufacturing process where applicable;
- availability/state facts required by the current workflow;
- optional explicit reference associations used only for candidate discovery.

For BQ, quantity is the accounted lot quantity owned by the Tool. Boquilhas consumes that quantity but does not own or mutate it.

For BQ in the current scope, the Tool/lote has one associated machine/line. This is Tool master data and may be used by Boquilhas configuration to resolve the repairer.

For CM, the supported manufacturing process values are:

```text
NNPB
PS
```

The process belongs to the Tool and is consumed by other workflows where required.

## CM reference assistance

CM may optionally carry an explicit MF/production-reference association for discovery when its own canonical reference differs from the production/MF reference.

Example:

```text
production / MF reference = 5810

CM 5810
→ direct reference candidate

CM 5809
→ may also be candidate if explicitly associated with MF reference 5810
```

The association is discovery assistance only. If CM 5809 is selected, its canonical reference remains 5809.

## Consultation and filtering

Ferramentas may expose registered Tools with focused server-side search/filtering.

Typical visible facts include:

- type;
- reference;
- lot;
- quantity where applicable;
- machine/line;
- process;
- state/availability;
- relevant technical values.

Filtering reduces candidates. It does not determine identity.

The application must not load every Tool merely to perform operational filtering in the browser when the backend can apply the focused query.

## Explicit selection

Tool selection is always explicit.

```text
search / filter
→ candidate Tool(s)
→ user explicitly selects one
→ canonical tool_id is returned
```

Even when only one candidate remains, the system must not silently select it.

The caller may use known production context to reduce the candidate set, but the selected persisted `tool_id` is always the result of the human choice.

Ferramentas returns the selected Tool identity to the calling workflow. It does not create production-context IDs such as `cm_id`, `mf_id` or `bq_id`.

## Contextual use

Ferramentas is commonly opened from another workflow such as Job On or from a consumer that needs a missing Tool value corrected/completed.

The calling workflow may:

1. open Ferramentas with useful filters/context;
2. inspect registered Tools;
3. explicitly select an existing Tool; or
4. create the missing Tool;
5. return the resulting `tool_id` to the original workflow.

Leaving a partially completed form to use Ferramentas must not silently discard unrelated unsaved values. Cancel returns without applying a new Tool. Success applies only the Tool explicitly selected/created.

## Tool creation

Creating a Tool creates a new canonical `tool_id`.

The user enters the Tool-owned facts required for that type. Optional technical values may be entered at creation or completed later.

Creating a Tool from Job On or another module still belongs to Ferramentas. The caller must not create a private Tool registry or mint its own `tool_id`.

## Tool duplication

Duplicate Tool is assisted creation of another real Tool.

```text
source tool_id T1
→ copy useful starting facts
→ user reviews/changes editable facts, including the required lot
→ create new tool_id T2
```

The source Tool remains unchanged.

Copied data is a starting default, not immutable inheritance. The user may correct the editable copied values before saving the new Tool.

A different lot must never be represented by mutating the source Tool identity.

## Delete boundary

Tool deletion may be exposed in the current scope, but deletion must never destroy or falsify operational history that already references that Tool.

The implementation must respect existing historical dependencies before physical removal. If historical truth requires the Tool to remain referencable, the deletion behavior must preserve that truth rather than cascading away production/control history.

## Technical values

Tool technical values are optional Tool-owned data keyed by the existing `tool_id`; they do not receive an independent canonical identity.

Current fields are:

### CM

- `volume_puncao`;
- `diametro_gargalo`;
- `peso_nominal`.

### BQ

- `volume_marisa`;
- `diametro_gargalo`.

### MF

- `diametro_gargalo`.

Technical numeric values use two decimal places where represented numerically.

A Tool may exist before all optional technical values are known.

Missing values are completed/corrected in Ferramentas on the same canonical `tool_id`.

Consumers do not create private editable copies of Tool-owned values merely because they need them.

The normal missing-value flow is:

```text
consumer requires Tool value
→ value missing
→ identify the Tool and missing field
→ user completes/corrects it in Ferramentas
→ consumer re-reads the Tool
→ workflow continues
```

## Historical stability of consumed values

Current Tool master/technical values remain owned by Ferramentas.

Where a completed operational record must reproduce exactly the value it consumed at that time, that consumer may freeze the exact consumed value as historical evidence.

That historical freeze does not create a second editable owner and later edits to the Tool must not rewrite already frozen historical inputs.

## Access

Ferramentas is contextual and need not exist as a mandatory top-level destination for every user.

Current access configuration may expose Tool creation/deletion broadly to authenticated DMO users according to the current product scope. That access decision must not be confused with the separate question of whether a historically referenced Tool may be physically removed.

## Rules that must not be violated

An implementation must not:

- derive `tool_id` from visible Tool attributes;
- silently auto-select a Tool;
- mutate an existing Tool into another lot;
- let Job On or another consumer create a competing Tool registry;
- let a consumer privately own an editable copy of Tool technical values;
- invent missing Tool values;
- use a CM technical-value source for a BQ-owned value or vice versa;
- create independent canonical IDs for the `tool_technical_values` extension;
- rewrite historical consumed values after later Tool edits;
- delete Tool history or downstream operational truth merely because Tool deletion is available in the UI.
