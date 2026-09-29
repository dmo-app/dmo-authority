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

Selecting a machine card opens the Boquilhas register/ficha for the BQ Tool currently associated with that machine.

The navigation follows the canonical Tool identity (`tool_id`) and the current Job On association. The card itself never becomes an authority for Tool identity.

## 3. Live values shown on the card

The card must expose, in real time, three operational values derived from the selected BQ register:

1. quantity currently **in house**;
2. quantity currently **out for repair**;
3. total accumulated **discrepancy** for the current production trace.

These values are read projections over the register facts. They are not independently editable balances and must not be stored as a second authority.

The discrepancy value follows the authoritative rules in `MOVIMENTOS.md`: it is the accumulated historical discrepancy of the current trace and is not automatically reconciled by later movements.

## 4. Automatic change when production changes

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

## 5. Card transition does not close the previous register

When a machine changes to a new production/BQ, the previous Boquilhas register is **not closed**.

It remains a valid historical/operational register and may continue to receive movements after it stops being the BQ displayed on the machine card.

For example, BQ sent to repair during the previous production may return after the machine has already started the next production. Those movements still belong to the previous register/trace where the operational event originated.

    machine card changed
    !=
    register closed

There is no required close action, acknowledgement, transfer action or extra lifecycle step when the machine card changes production.

## 6. Separation of concerns

The implementation must preserve these distinct concepts:

    Job On schedule
    → determines which BQ is current on a machine

    Machine side panel
    → navigation + live visual projection

    Boquilhas register
    → durable operational history

    Movement facts
    → continue to belong to their register regardless of which BQ is currently shown on the machine card

The side panel must never alter register history merely because the current machine assignment changed.

## 7. Explicitly forbidden interpretations

An implementation must not:

- close a Boquilhas register when its machine card changes to another BQ;
- prevent movements on the previous register merely because it is no longer current on the machine;
- move historical movements to the new production;
- reset the previous register when a new Job On becomes current;
- require a manual handover action solely to update the machine card;
- treat the side panel as persistence authority;
- store the three card values as independent mutable balances;
- infer that disappearance from the side panel means the register is finished.

The card is a live operational guide and access path. It does not change the semantics or lifecycle of the register.
