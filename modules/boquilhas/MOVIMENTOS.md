# Boquilhas — Movimentos

This file defines the movement discrepancy behavior of the Boquilhas module.


## Implementation boundary — one movement trace per BQ production context

This rule evolves the Boquilhas movement behavior already implemented in the application.

The existing `boquilhas_id`-based register/persistence is a valid implementation base and its real operational history must be preserved.

The canonical movement boundary is `bq_repair_trace_id`.

One `bq_id` / production context has one repair trace, and that trace groups all Boquilhas movement cycles for that production. A new `saida` does not create another trace.

A trace may begin before Job On from the canonical BQ `tool_id`, with `bq_id` unresolved. At most one such unresolved trace may exist at a time for the same `tool_id`. The same Tool may nevertheless have many traces historically because each associated production has its own `bq_id` and trace. When Job On later creates the matching `bq_id` referencing the same canonical `tool_id`, the pending trace is associated automatically. That association does not replace the trace, move its existing movements, or reset its history. No separate open/closed trace state is required.

Implementation should adapt/reconcile the existing register model rather than destroy valid data merely to rename persistence.

## 1. Operational truth has priority over mathematical reconciliation

Boquilhas must preserve what physically happened, including operational inconsistencies.

The system must **not** reject, clamp, invent, compensate or silently rewrite a real movement merely because the movement cannot be fully explained by the preceding history.

A mathematically tidy ledger is not more important than the observed physical event.

## 2. Matched quantity and unexplained quantity

For a return movement, the system first determines how much of the returned quantity can be explained by the quantity that was legitimately out of the lot in that trace.

Example:

```text
Saída:   5
Entrada: 7

Matched:       5
Discrepância: -2
```

The five matched BQ return to the normal flow.

The two additional BQ are physically observed, so the movement is recorded in full, but those two **must not increase the accounted total of the lot**. They are an unexplained operational discrepancy.

The system must never make the lot become 102 merely because 102 physical BQ were observed around a lot whose accounted total is 100.

## 3. Meaning of "Saldo" in the movement table

The **Saldo** column is not stock, outstanding repair quantity, or a conventional running accounting balance.

It is a **per-movement discrepancy indicator**.

When a movement has no discrepancy:

```text
Saldo = blank
```

The UI must not display `0`. An empty cell is intentional so that exceptional values attract immediate attention.

When an Entrada contains quantity that cannot be explained by the trace history:

```text
Saldo = -unmatched_quantity
```

Example:

```text
Movimento     Quantidade     Saldo

Saída             5
Entrada            5

Saída             5
Entrada            7          -2
```

The visible negative value is an alert that this movement introduced BQ that cannot be counted inside the normal lot total.

## 4. Discrepancies are historical facts, not debts to reconcile

A discrepancy is never automatically cancelled, compensated or corrected by a later movement.

If four movements produce:

```text
-2
-1
-4
-1
```

the trace discrepancy is:

```text
-8
```

A later movement that happens to create the opposite numerical situation does **not** reduce that `-8`.

The discrepancy records that the exceptional event happened. It is not a temporary account waiting for another event to make it zero.

Therefore:

```text
trace_discrepancy = sum(all movement discrepancies in this trace)
```

where normal movements contribute no discrepancy and are visually blank in the Saldo column.

## 5. Scope and reset

The accumulated discrepancy belongs to one repair trace identified by `bq_repair_trace_id`.

It is scoped to the trace of one `bq_id` / production and is not a lifetime balance of the physical BQ `tool_id`.

A repair trace may:

```text
begin from tool_id before Job On
→ later associate to bq_id
→ keep the same bq_repair_trace_id
→ continue receiving later movements
```

Association to production does not reset the trace.

A **new BQ production context** uses a new trace with:

```text
trace_discrepancy = 0
```

Previous traces retain their historical discrepancy unchanged.

A new production has its own `bq_id` and trace. That change must never migrate old movements or discrepancies out of the previous trace.

## 6. Required implementation behavior

The backend must preserve two separate concepts:

1. the normal accounted movement of BQ that can be explained by the trace; and
2. the unexplained quantity observed on a movement.

For an Entrada whose quantity exceeds the explainable return quantity:

```text
matched_quantity   = explainable portion
unmatched_quantity = observed quantity - matched_quantity
movement_discrepancy = -unmatched_quantity
```

Only the matched portion may return to the accounted lot quantity.

The unmatched portion remains recorded as part of the real movement but stays outside the accounted lot total.

The trace-level discrepancy is derived from the discrepancies of the movements that belong to that trace. It must not be derived as a simple signed sum of all Saída and Entrada quantities.

## 7. Explicitly forbidden interpretations

An implementation must not:

- reject an Entrada only because its quantity exceeds the currently explainable return quantity;
- truncate the observed Entrada to the expected quantity;
- increase the nominal/accounted lot total to absorb the excess;
- invent a missing Saída to make the numbers reconcile;
- show `0` in the Saldo column for ordinary movements;
- use a later movement to cancel an earlier discrepancy;
- move or carry a discrepancy from one repair trace into another merely because production changes;
- attach repair movements directly to `bq_id` as one undifferentiated lifetime movement list;
- implement Saldo as `Σ Saída - Σ Entrada` or another conventional running stock balance.

## 8. Acceptance examples

### Normal flow

```text
Saída 5
Entrada 5
```

Result:

- Entrada fully matched.
- Movement Saldo is blank.
- Trace discrepancy remains unchanged.

### Exceptional Entrada

```text
Saída 5
Entrada 7
```

Result:

- 5 are matched.
- 2 are recorded as unexplained.
- Movement Saldo shows `-2`.
- Those 2 do not increase the accounted lot quantity.
- Trace discrepancy gains `-2`.

### Several exceptional movements

```text
Movement A: -2
Movement B: -1
Movement C: -4
Movement D: -1
```

Result:

```text
Trace discrepancy: -8
```

None of the four historical discrepancies is cancelled by another movement.

### New production trace

Previous repair trace:

```text
Trace discrepancy: -8
```

New repair trace:

```text
Trace discrepancy: 0
```

The previous `-8` remains historical evidence on the previous `bq_repair_trace_id`.

A new production uses a different trace, but it does not rewrite, transfer or reset the previous trace; late returns remain on the previous trace.

## 9. Editing boundary

This rule defines movement creation, display and trace accumulation.

Editing an existing movement must never be used as an automatic reconciliation mechanism. Any future rule governing whether an explicit human edit may alter the discrepancy originally produced by that same movement must be defined separately rather than inferred.
