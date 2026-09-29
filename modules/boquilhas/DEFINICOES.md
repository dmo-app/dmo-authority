# Boquilhas — Definições

Boquilhas Definições contains configuration used by the Boquilhas operational workflow.

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
