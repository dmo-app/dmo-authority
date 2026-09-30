# Boquilhas — Definições

Boquilhas Definições contains configuration used by the Boquilhas operational workflow.

## Production activation time

Boquilhas owns its own configurable daily production-activation time.

This setting determines when Boquilhas adopts the next planned Job On production context for each machine.

Conceptually:

```text
planned production-transition awareness
-> may already be pending

Boquilhas production-activation time arrives
-> Boquilhas reads Job On
-> resolves the applicable production/BQ context
-> machine card switches to that context
```

This is a Boquilhas setting, not a global application time.

It must not be interpreted as the time when every other module changes production.

It also does **not** delay awareness of a change inside the same `jobon_id`. If the BQ context of the production Boquilhas is already using changes, Boquilhas receives that context-change awareness immediately and revalidates the Job On without waiting for the next scheduled production-activation time.

Changing the configured activation time affects how Boquilhas handles planned production transitions. It must not rewrite historical movements or reassign records that already belong to an earlier production context.

## Repairers

The module maintains the repairer register used when recording applicable repair movements.

## Machine assignments

Repairer assignment is configured independently for the production machines:

- B1;
- B2;
- B3;
- C1;
- C2;
- C3.

Machines are not grouped for this assignment rule.

A machine may have no current assignment ("Sem associação").

## Historical rule

Changing a current machine→repairer assignment must not rewrite historical movements.

When a movement requires a repairer, the selected/resolved repairer used for that movement is preserved with the movement facts required by the implementation.

Definições provides configuration to the workflow; it is not a replacement identity or history store.
