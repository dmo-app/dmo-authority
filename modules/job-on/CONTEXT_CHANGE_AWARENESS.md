# Job On — Context Change Awareness

## Purpose

This document defines the functional v1 mechanism for making relevant changes to an existing Job On production context visible to the modules and people that depend on that context.

The goal is awareness, not automatic operational decision-making.

The minimum behavior is:

```text
Job On context changes
-> permanent change log
-> awareness for relevant consumers
-> module acknowledgement when seen
```

No additional resolution workflow is implied.

---

## 1. Two different events must remain distinct

### Current production changed

This means a machine is now running a different production:

```text
machine = B1

previous jobon_id
-> current jobon_id
```

This is a change of current production.

### Job On context changed

This means the same production occurrence remains identified by the same `jobon_id`, but a relevant fact inside that production was replaced:

```text
jobon_id = unchanged

<old_cm_id> -> <new_cm_id>
<old_mf_id> -> <new_mf_id>
<old_bq_id> -> <new_bq_id>
old TP/Calote value -> new TP/Calote value
```

These events are related operationally but are not the same event and must not be modeled as interchangeable.

This document defines **Job On context changed** awareness.

---

## 2. Production identity and replacement contexts

A substitution inside the same production does not create a replacement `jobon_id`.

For Tool-backed production contexts, the old context identity is never mutated so that it points to another physical Tool.

Forbidden:

```text
existing bq_id
tool_id X -> tool_id Y
```

Required model:

```text
<old_bq_id> -> tool_id X
<new_bq_id> -> tool_id Y
```

The same principle applies to `cm_id` and `mf_id`.

Therefore:

- the `jobon_id` may remain the same;
- the replacement Tool receives a new production-context identity;
- the previous context remains referencable;
- downstream records that already use the previous context remain historically truthful.

Notation rule for documentation:

```text
cm_id -> <old_cm_id> / <new_cm_id>
mf_id -> <old_mf_id> / <new_mf_id>
bq_id -> <old_bq_id> / <new_bq_id>

machine -> B1 / B2 / B3 / C1 / C2 / C3
```

Machine labels such as B1/B2/C1 must not be reused as fictional component-context IDs in examples.

---

## 3. The mechanism has three pieces

```text
JOB ON CHANGE LOG
= permanent memory of what changed

PING / AWARENESS
= draws attention to a relevant change

MODULE ACKNOWLEDGEMENT
= "seen / taken notice of"
```

These three concepts must remain separate.

### Acknowledgement means only "seen"

A module acknowledgement does **not** mean:

- resolved;
- corrected;
- recalculated;
- approved;
- operationally treated.

It means only that the change was seen/taken notice of.

Acknowledgement never deletes or rewrites the Job On change log.

Acknowledgement is scoped to the consuming module. If the same change matters to more than one module, acknowledgement by one module must not clear another module's pending awareness.

If nobody has acknowledged a relevant change for a module, the awareness remains available when that module is opened later. The mechanism must not depend on a transient on-screen event being witnessed at the exact time of the change.

---

## 4. Permanent Job On change log

When a relevant Job On fact changes, Job On records the change permanently.

For a BQ replacement the conceptual fact is:

```text
jobon_id
changed_context = BQ
<old_bq_id> -> <new_bq_id>
actor
timestamp
```

Equivalent context identifiers apply to CM and MF changes.

The log is the durable operational memory. It remains available after acknowledgement.

The user-facing presentation does not need to expose UUIDs. It may show the human information required to understand the change, for example:

```text
Job On 5447T173
BQ alterada
Lote anterior: 123
Novo lote: 147
```

Human-readable labels are presentation. Canonical IDs remain the identity used by persistence and relationships.

---

## 5. Lightweight awareness signal

The awareness signal is deliberately small.

Conceptually:

```text
jobon_id
context_changed = BQ
```

or the corresponding CM, MF, TP/Calote or other relevant production context.

It does not carry a complete Job On snapshot and does not become a second source of production truth.

The normal module/backend relationship already knows how to read the current production context it needs.

The meaning of the signal is only:

> Something in the Job On production context that this consumer depends on changed.

---

## 6. Consumers come from functional dependencies

DMO must not create a separate notification-routing model that duplicates the operational dependency model.

The question is:

> Which Job On production context does this module/function already depend on?

That same dependency determines whether a context change should be exposed to that consumer.

Conceptually:

```text
production-context dependency
-> consumer

context changes
-> same consumer receives awareness
```

Notification routing must not become a separate source of domain truth.

The exact consumer mapping for CM, MF, BQ, TP/Calote and other production facts is refined with the functional dependency documentation of the consuming modules. That mapping is not invented in this mechanism.

---

## 7. Role of the application

This is an awareness feature, not an automation engine.

The transverse mechanism does not automatically:

- decide the operational impact of the change;
- create work;
- decide that a new Peso is required;
- recalculate Peso;
- resolve Boquilhas;
- create resolution workflows;
- mark operational work as treated;
- rewrite historical records.

The minimal flow remains:

```text
change
-> awareness
-> human verifies
-> acknowledgement when seen
```

A module may still have its own independent domain behavior. For example, Boquilhas may use its own canonical identity rules to associate a pre-production trace to a later BQ production context. That behavior is not performed by the generic Job On awareness mechanism.

---

## 8. Historical truth is preserved

A context change never rewrites a downstream record that already belongs to the previous context.

For example:

```text
existing record
-> <old_cm_id>

Job On later changes CM
-> <new_cm_id>
```

The existing record keeps `<old_cm_id>`.

The awareness mechanism tells relevant consumers that the Job On context changed. It does not move the existing record to the new context.

The same principle applies to BQ repair traces, movement history and other context-bound operational records.

---

## 9. Machine changes

A machine change is not currently treated as equivalent to an urgent CM/MF/BQ context-awareness alert.

Machine planning changes may still be recorded as normal Job On changes, but this v1 does not introduce an equivalent red-attention rule for them.

If a real operational case later requires stronger awareness for machine changes, that behavior may be added explicitly without changing this core mechanism.

---

## 10. Functional v1 boundary

The mechanism is conceptually closed at this level:

```text
1. a real Job On context change is recorded
2. the Job On change log is permanent
3. a lightweight awareness signal identifies jobon_id + changed context
4. relevant consumers are derived from existing functional dependencies
5. pending awareness remains visible until that module acknowledges it
6. acknowledgement means only "seen"
7. acknowledgement does not remove the log
8. historical operational records are never rewritten by awareness
```

Still to refine with module dependency documentation:

```text
CM -> actual consumers
MF -> actual consumers
BQ -> actual consumers
TP/Calote -> actual consumers
other production facts -> actual consumers
```

That refinement does not reopen the awareness mechanism itself.

## Core rule

> **When a Job On production context used by another module changes, the change is permanently logged and a lightweight awareness notification is exposed to the consumers of that context. Acknowledgement means only that the change was seen; it does not resolve, correct, recalculate or rewrite operational history.**

> **The consumers of a change are derived from the same production-context dependencies used by the operational workflows; notification routing must not become a separate source of domain truth.**
