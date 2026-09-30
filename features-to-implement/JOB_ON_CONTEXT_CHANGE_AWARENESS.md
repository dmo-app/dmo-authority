# Job On Context Change Awareness

**Status:** FUNCTIONALLY DEFINED — NOT IMPLEMENTED

**Type:** Cross-cutting implementation feature

## Canonical functional source

The complete functional behavior belongs to:

- `modules/job-on/CONTEXT_CHANGE_AWARENESS.md`

Consumer-specific configuration/behavior belongs to the relevant owning module, for example:

- `modules/boquilhas/DEFINICOES.md`
- `modules/boquilhas/REGISTO.md`

This feature file is an implementation/review checklist. It must not become a second copy of the functional rule.

If wording here and the canonical Job On document ever differ, the module blueprint is the functional source.

## Implementation scope

Implement one awareness capability that preserves two distinct event semantics:

```text
PRODUCTION_TRANSITION
-> may be known before activation
-> consumer reacts at its own configured production-activation time
-> consumer re-reads Job On at that time
-> acknowledgement before activation does not complete/cancel the scheduled transition

CONTEXT_CHANGED
-> same jobon_id
-> relevant CM/MF/BQ/TP or other consumed context changes
-> consumer awareness is immediate
-> consumer re-reads Job On immediately
```

The implementation may share transport/storage infrastructure for both event kinds, but it must preserve their different timing behavior.

## Required implementation properties

The implementation must provide:

- permanent Job On change history for real same-`jobon_id` context changes;
- lightweight awareness rather than a duplicated Job On snapshot;
- an event-kind distinction equivalent to `PRODUCTION_TRANSITION` vs `CONTEXT_CHANGED`;
- per-consumer pending/acknowledgement state where acknowledgement is used;
- module-owned production-activation configuration for consumers that use planned transitions;
- re-reading of Job On when the consumer must react;
- preservation of historical records on their original CM/MF/BQ/other production contexts;
- consumer routing derived from the real production-context dependencies rather than a second independent notification map.

For a planned transition, any `jobon_id` stored/carried with the awareness must not be treated as the final production truth at activation time. The consumer re-reads Job On and uses the then-applicable plan.

## Acknowledgement boundary

A reviewer must distinguish:

```text
awareness acknowledged
!=
production transition applied
```

A user/module may see or acknowledge a future `PRODUCTION_TRANSITION` before the configured activation time.

That must not remove or cancel the consumer's obligation to perform the scheduled Job On re-read when its activation time arrives.

For `CONTEXT_CHANGED`, acknowledgement still means only that the immediate change awareness was seen. It does not mean corrected, recalculated, approved or operationally resolved.

## Technical choices intentionally left open

Planning/Architect may choose the technical representation for:

- change-log persistence;
- awareness/event persistence;
- per-consumer acknowledgement state;
- scheduling mechanism;
- delivery/revalidation mechanism;
- storage of each consumer module's activation time;
- UI treatment of pending/seen awareness;
- retention/query UX for the permanent change history.

The blueprint does not require an event bus, message bus, polling implementation or a particular scheduler.

## Dependency work still required

Before implementation is considered complete, the real consumer map must be confirmed from the owning workflows for:

```text
CM -> actual consumers
MF -> actual consumers
BQ -> actual consumers
TP/Calote -> actual consumers
other production facts -> actual consumers
```

Do not create a separate routing truth merely for awareness.

## Acceptance / reviewer checks

Reject an implementation that:

- treats `PRODUCTION_TRANSITION` and `CONTEXT_CHANGED` as having the same timing;
- uses midnight, `07:00` or another global application time for every consumer;
- lets one module's activation time control another module;
- lets acknowledgement of a future production transition cancel or complete the scheduled activation;
- trusts an old transition payload instead of re-reading Job On at activation time;
- delays a same-`jobon_id` context change until a later scheduled activation time;
- mutates an existing CM/MF/BQ context to point at a replacement Tool;
- rewrites historical Peso, Boquilhas traces or other existing records onto the new context;
- deletes the permanent same-Job-On change fact when awareness is acknowledged;
- interprets acknowledgement as correction, recalculation, approval or resolution;
- duplicates a full Job On snapshot into the awareness system without a separately justified need;
- introduces a notification-routing map that can disagree with the actual operational dependency model.

## Completion rule

This feature may be marked implemented only when:

1. the selected app baseline implements both event timings correctly;
2. the consuming modules that need planned transitions own/configure their own activation times;
3. immediate context-change consumers revalidate without waiting for those scheduled times;
4. acknowledgement and scheduled application are separate states/concerns;
5. historical identities and records remain truthful;
6. tests cover both event kinds and the acknowledgement-vs-activation distinction.
