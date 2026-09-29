# Controlo Approve — Overview

Controlo Approve is the review and decision surface of the broader Controlo domain.

It does not create a second copy of the underlying control record.

Where a workflow is approvable, Create and Approve operate on the same durable record identity.

## Responsibilities

Controlo Approve may:

- review submitted control information;
- approve;
- reject with the required reason where applicable;
- reopen where the workflow allows it;
- expose the decision history.

## Shared context

The approval surface reads the same production and control context as the create side.

Shared Controlo context rules belong in `../controlo/OVERVIEW.md`.

UI parity or a shared read model does not create a new persistence identity.
