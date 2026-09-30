# Controlo Create — Comparação

Comparação belongs entirely to Controlo Create.

There is no separate Comparação approval workflow.

## Purpose

Comparação is an optional workflow performed against an already decided Peso.

It allows one or more CM subjects from the same production context to be re-measured during production in order to verify consistency or investigate a deviation.

Comparação complements the original Peso. It does not replace, revise or rewrite it.

This workflow is distinct from the **previous-production difference shown in the normal Peso flow**. The normal Peso historical difference compares the current Peso/CM results with the previous eligible production for display/support; it does not create a `comparacao_id` and is not this workflow.

## Flow

1. Start a Comparação against an existing decided `peso_id`.
2. Allocate a new `comparacao_id`.
3. Add one or more CM subjects by reusing their existing production `cm_id` values.
4. Record the required comparison measurements using the applicable facts already established by the referenced Peso/context.
5. Decide each measured CM explicitly:
   - `Manter`;
   - `Colocar de parte`.
6. Confirm the Comparação when every measured CM subject has a final decision.

`Colocar de parte` requires a non-empty justification.

The application must not infer either decision from warnings, measurements or calculated results.

Calculated comparison results may legitimately be negative. A negative derived result is a valid signed result and must not be clamped, converted to zero, or treated as invalid merely because of its sign.

## Identity and relations

- Comparação has its own `comparacao_id`.
- It references one existing `peso_id`.
- It reuses existing `cm_id` values; it does not create new CM identities.
- A Comparação may contain one or several CM subjects.
- The subject inside one Comparação is identified by the pair `(comparacao_id, cm_id)`.
- Multiple Comparação events may exist for the same Peso.
- Multiple events may legitimately include the same `cm_id`.

Comparação does not create a `previous_peso_id` relation. It is not a comparison between two Peso records.

## Measurement boundary

Comparison measurements belong to the Comparação event.

They must remain distinguishable from the measurement rows of the original Peso.

Where the comparison needs conditions or technical values already frozen/established by the referenced Peso, it reuses those truthful facts rather than inventing new conditions.

The original Peso remains unchanged.

## Per-CM decisions

Each measured CM subject receives its own explicit decision.

Within one Comparação event:

- a measured CM must have a final decision before the event can be confirmed;
- `Manter` requires no justification;
- `Colocar de parte` requires a non-empty justification;
- the same CM subject must not receive a second conflicting final decision inside that event.

Confirmation records the completion of the event after all measured subjects are decided.

## What Comparação does not do

Comparação does not modify the original Peso.

It does not rewrite the Peso's:

- measurements;
- calculated results;
- averages;
- status;
- approval decision;
- frozen facts;
- generated/derived outputs.

It does not create new CM identities and does not require every CM in the production to participate.

One or several CMs may be compared according to the real operational need.

## History

Each Comparação event has its own `comparacao_id`.

History accumulates by creating additional events rather than overwriting earlier ones.

This allows the same Peso and the same `cm_id` to participate in more than one Comparação over time while preserving each event independently.
