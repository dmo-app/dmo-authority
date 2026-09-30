# Boquilhas — Registo

This file is authoritative for the Boquilhas Registo surface and its machine side panel.

## 1. The machine side panel is navigation and live operational context

The machine side panel in Boquilhas is **not part of the lifecycle of a Boquilhas register**.

Its purpose is:

- fast access to the BQ currently associated with each production machine;
- a visual guide showing which BQ is currently on each machine;
- a compact live view of important values derived from that BQ register.

Being shown, replaced or no longer shown in a machine card does **not** create, close, archive, reset or otherwise mutate the underlying Boquilhas register.

## 2. Machine cards

The side panel contains the production machines (B1, B2, B3, C1, C2, C3).

Each machine card resolves the BQ that is current for that machine from the Job On production flow.

The card is therefore a projection of the current production assignment, not an ownership relation and not a stored state on the Boquilhas register.

The machine card is also a direct operational shortcut.

A **double-click** on the card opens the Boquilhas registration workflow already contextualized with the correct BQ Tool for that machine. The user must not have to navigate to Registo, search manually by reference/lote, and then select the Tool again.

The shortcut carries the canonical `tool_id` resolved from the current Job On/BQ association into the registration flow so the correct Tool is already selected and ready for the user to register the operation.

This shortcut removes navigation/search work only. It does not create a second Tool-selection authority, does not infer a Tool from text such as reference/lote, and does not change any Boquilhas register semantics.

The navigation follows the canonical Tool identity (`tool_id`) and the current Job On association. The card itself never becomes an authority for Tool identity.

## 3. The machine card is NOT the only way to register

The machine shortcut is an optimization for the BQ that is currently in production on that machine.

It must never become the only entry path into Boquilhas registration.

The **Registo** tab remains an independent operational entry point where the user can search/select a BQ Tool and then:

- create a Boquilhas repair trace for a BQ that has not yet entered production and therefore has no current machine card;
- open an existing register/repair trace for a BQ that is no longer the current BQ shown on a machine;
- record later movements against that older register, including returns from repair that happen after the machine has already changed to the next production.

Example:

    Production A uses BQ-X.
    Near the end of Production A, 5 BQ-X are sent to repair.

    Production B starts and the machine card now shows BQ-Y.

    One or two days later, the repaired BQ-X return.

The user must still be able to go to Registo, find BQ-X / its existing repair trace, and record the Entrada against that same `bq_repair_trace_id`.

The fact that BQ-X is no longer visible as the current machine card must not block or redirect the movement to BQ-Y.

For a BQ not yet associated with a Job On, Registo may begin from the canonical BQ Tool identity (`tool_id`) and create/preserve the future production trace with `bq_id = null`. When Job On later creates a `bq_id` that references the same canonical `tool_id`, that same pending trace is associated automatically when the match is unambiguous. The association does not create a replacement trace or move its existing movements. While `bq_id` is unresolved, Registo shows a persistent Job On association warning derived from that missing association.

Therefore the module has two valid entry patterns:

    CURRENT PRODUCTION
    machine card double-click
    → current Job On/BQ association
    → correct tool_id already selected
    → open the trace for that bq_id / production
    → register movement

    GENERAL / NON-CURRENT
    Registo tab
    → search/select canonical BQ Tool or existing register/trace
    → create/open the applicable production trace
    → register movement

Both paths reach the same Boquilhas registration semantics. The machine-card path is only faster because the context is already known.

## 4. Live values shown on the card

The card must expose, in real time, three operational values derived from the selected BQ register:

1. quantity currently **in house**;
2. quantity currently **out for repair**;
3. accumulated **discrepancy** for the relevant repair trace context associated with the current BQ.

These values are read projections over the register facts. They are not independently editable balances and must not be stored as a second authority.

The discrepancy value follows the authoritative rules in `MOVIMENTOS.md`: it is the accumulated historical discrepancy of the current trace and is not automatically reconciled by later movements.

## 5. Automatic change when production changes

The BQ shown on a machine card follows the Job On production schedule automatically.

Example:

    Machine: B1

    29/09/2026
    → card shows the BQ associated with the current B1 production

    30/09/2026 at 07:00 local time
    → the new B1 Job On becomes the current production
    → the B1 card automatically points to the BQ associated with that new production

No user action is required to perform this card transition.

The transition is part of normal operational flow.

## 6. Card transition does not close the previous register

When a machine changes to a new production/BQ, the previous Boquilhas register is **not closed**.

It remains a valid historical/operational register and may continue to receive movements after it stops being the BQ displayed on the machine card.

For example, BQ sent to repair during the previous production may return after the machine has already started the next production. Those movements still belong to the same `bq_repair_trace_id` where the repair process originated.

    machine card changed
    !=
    register closed

There is no required close action, acknowledgement, transfer action or extra lifecycle step when the machine card changes production.

## 7. Separation of concerns

The implementation must preserve these distinct concepts:

    Job On schedule
    → determines which BQ is current on a machine

    Machine side panel
    → navigation + live visual projection for the current production

    Registo tab
    → general entry point for current, previous and pre-production BQ registration

    Boquilhas register
    → durable operational access / existing persistence base

    bq_repair_trace_id
    → one trace for one bq_id / production context
    → groups all movement cycles of that production
    → may temporarily exist from tool_id with bq_id unresolved before Job On

    Movement facts
    → belong to their repair trace regardless of which BQ is currently shown on the machine card

The side panel must never alter repair-trace history merely because the current machine assignment changed. Each new production has its own `bq_id` and trace; late movements remain on the earlier trace where they originated.

## 8. Explicitly forbidden interpretations

An implementation must not:

- make the machine card the only way to create or access a Boquilhas register;
- require a BQ to be currently on a production machine before a register can exist;
- block a movement because the BQ is no longer the current card on that machine;
- redirect a late return from a previous production to the BQ currently shown on the machine;
- close a Boquilhas register when its machine card changes to another BQ;
- prevent movements on the previous register merely because it is no longer current on the machine;
- move historical movements to the new production;
- move movements from their `bq_repair_trace_id` onto `bq_id` merely for navigation convenience;
- reset the previous register when a new Job On becomes current;
- require a manual handover action solely to update the machine card;
- treat the side panel as persistence authority;
- store the three card values as independent mutable balances;
- infer that disappearance from the side panel means the register is finished.

The card is a live operational guide and access path. It does not replace the Registo tab and does not change the semantics or lifecycle of the register.
