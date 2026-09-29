# Controlo Approve — Aprovar

Approval acts on the existing durable record.

It must not create an approval copy.

## Peso

For Peso, the same `peso_id` persists through the decision lifecycle.

Available decisions include:

- approve;
- reject;
- reopen where permitted.

A rejection requires a non-blank reason.

Decision events must preserve who decided, when the decision occurred and the relevant prior/new state required by the workflow.

## Reopen

Reopen returns the record to the editable/submittable state defined by that workflow.

It does not erase the previous decision event.

The history remains evidence that the earlier decision occurred.

## Human decision

Calculated values, alerts and technical NOK/OK states may inform the reviewer but must not silently choose the approval decision.
