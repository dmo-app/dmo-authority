# Controlo Approve — Overview

Controlo Approve is the review and decision capability of the broader Controlo domain.

It does not create a second copy of the underlying control record.

Where a workflow is approvable, Create and Approve operate on the same durable record identity and the same underlying production/control state.

## Responsibilities

Controlo Approve may:

- review submitted control information;
- read the current Folha/control state;
- read the current Resumo composition;
- approve;
- reject with the required reason where applicable;
- reopen where the workflow allows it;
- expose the decision history.

## Read-only operational boundary

Controlo Approve does not edit the operational content created/maintained through Controlo Create.

Seeing the same Folha, Resumo, measurements, observations or technical facts does not grant ownership to change them.

Approve writes only approval-side facts defined by the approval workflow, such as the decision, required reason/context, actor, timestamp and decision history.

Conceptually:

```text
Create writes/edits operational control state
-> Approve reads that same updated state

Approve
-> writes approval decision/history
-> does not rewrite Create-owned operational facts
```

## Shared context

The approval surface reads the same production and control context as the create side.

Folha and Resumo are shared surfaces over that same state; they are not separate Approve copies.

Shared Controlo context rules belong in `../controlo/OVERVIEW.md`.

UI parity or a shared read model does not create a new persistence identity.
