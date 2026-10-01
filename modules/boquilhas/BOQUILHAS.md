# Boquilhas

Boquilhas records the real repair movements of canonical BQ Tools and preserves those movements in the production context where they occurred.

This file is the single functional source for the Boquilhas module. Backend and frontend material may add implementation or presentation detail, but must not redefine these rules.

## Identities and relationship

```text
tool_id
= canonical BQ Tool / lot

Tool.quantity
= accounted quantity of physical BQ tools represented by that Tool / lot

jobon_id
→ bq_id
→ one bq_repair_trace_id
→ many movement_id
```

`tool_id` is the canonical Tool identity owned by Ferramentas.

`bq_id` identifies that BQ Tool in one production context.

`bq_repair_trace_id` identifies the repair-movement trace for that BQ production context. It is not one repair trip. One trace may contain several repair cycles.

`movement_id` identifies one real repair movement inside the trace.

A new production receives a new `bq_id` and a new repair trace, even when it uses the same canonical `tool_id` as an earlier production.

There is no trace `open` / `closed` lifecycle.

## Tool quantity and machine association

The fixed lot quantity belongs to the canonical BQ Tool and is entered in Ferramentas.

Example:

```text
BQ Tool
lot = 4
quantity = 120
```

Boquilhas consumes that quantity as the accounted lot base. Repair movements never mutate it.

A BQ Tool/lote also carries its associated production machine/line. That association is used to resolve the configured repairer, including before the Tool has entered a Job On production.

The machine association does not mean that the Tool is currently in production on that machine.

## Repair trace before and after Job On

A repair movement may exist before the relevant Job On exists.

In that case:

```text
tool_id
→ one unresolved bq_repair_trace_id
→ movements

bq_id = null
```

For one BQ `tool_id`, at most one unresolved pre-production trace may exist at a time.

If that unresolved trace already exists, later movements for the same Tool continue in it. A second unresolved trace is not created.

When the BQ Tool is explicitly selected in Job On:

```text
selected BQ tool_id
→ bq_id created/resolved
→ same bq_repair_trace_id associates to bq_id
→ direct trace.tool_id is cleared
→ existing movements remain on the same trace
```

The explicit Tool selection is already the association decision. There is no second association prompt.

After association, the Tool remains reachable through:

```text
bq_repair_trace_id
→ bq_id
→ tool_id
```

If no pre-production trace exists when the production `bq_id` is created, that production uses a new trace. If the trace begins after `bq_id` already exists, it associates directly to that production context.

## Registo and machine side panel

Boquilhas has two valid entry paths.

### Current production

The machine side panel shows the BQ currently associated with each production machine:

- B1;
- B2;
- B3;
- C1;
- C2;
- C3.

The card is navigation and a live projection of Job On context. It is not persisted Boquilhas state and it does not own the BQ identity.

A double-click on a machine card opens Registo already contextualized with the canonical BQ Tool resolved from the current Job On/BQ association.

The shortcut removes search/navigation work only. It does not infer identity from labels and does not alter repair-trace semantics.

### General / non-current registration

Registo remains independently accessible for:

- a BQ that has not yet entered production;
- an older BQ that is no longer current on a machine;
- late returns from an earlier production;
- any existing trace that still needs movements recorded.

The machine side panel must never become the only way to reach a repair trace.

## Live values

The machine card may show, in real time:

1. quantity in house;
2. quantity out for repair;
3. accumulated discrepancy for the applicable repair trace.

These values are derived from the fixed Tool quantity plus real movement facts. They are not independently editable balances and must not become a second source of truth.

## Production transition

A future Job On may already exist as planning information without becoming the current Boquilhas production.

Boquilhas owns a configurable daily production-activation time.

```text
future Job On exists
→ planning information only

Boquilhas activation time arrives
→ Boquilhas re-reads Job On
→ resolves the production/BQ context that now applies
→ machine card switches to that context
```

This is a Boquilhas setting, not a global application time.

A same-`jobon_id` BQ context change is different: if the production context already being consumed changes, Boquilhas must re-read Job On immediately and must not wait for the next scheduled activation time.

Changing machine-card context does not close, archive or reset the previous repair trace.

## Movement types

Allowed movement types are:

- `saida`;
- `entrada`;
- `entrada_sem_reparacao`.

A new `saida` does not create a new repair trace.

### Saída

A Saída records BQ physically sent to the repairer resolved from Boquilhas configuration.

A real observed Saída may exceed the quantity currently derived as in house. The movement is recorded in full rather than rejected, clamped or rewritten.

An exceptional Saída does not create the Entrada discrepancy/Saldo described below.

### Entrada

An Entrada records the quantity physically returned.

If the observed Entrada exceeds the quantity that can legitimately return at that point in the trace, the full Entrada is still recorded.

```text
matched_quantity
= explainable return portion

unmatched_quantity
= observed Entrada - matched_quantity

movement_discrepancy
= -unmatched_quantity
```

Example:

```text
Saída:   5
Entrada: 7

matched = 5
extra = 2
Saldo = -2
```

The extra quantity does not increase the fixed Tool quantity.

### Entrada sem reparação

`entrada_sem_reparacao` means BQ returned without having been repaired.

It reduces the quantity out for repair according to the real movement flow.

It is not itself a discrepancy classification.

## Saldo and trace discrepancy

Saldo is a per-movement Entrada discrepancy indicator.

It is not stock, quantity out for repair or a conventional running balance.

```text
normal movement
→ Saldo = blank

exceptional Entrada
→ Saldo = -extra Entrada quantity
```

Do not display `0` for an ordinary movement.

A later normal movement does not cancel an earlier discrepancy.

```text
trace_discrepancy
= sum(all movement discrepancies in this trace)
```

Each trace keeps its own discrepancy history. A new production starts a different trace and does not inherit or absorb the previous trace's discrepancy.

## Late returns and history

A later production never takes ownership of an earlier trace.

Example:

```text
Production A
→ bq_id A
→ trace A
→ saida 5

Production B
→ bq_id B
→ trace B

late entrada 5
→ trace A
```

The current machine card is not used to reassign historical movements.

The same canonical BQ Tool may therefore have many repair traces over time, one for each applicable production context, while still allowing one unresolved pre-production trace before association.

## Correction and removal

Movement calculations depend on sequence.

Only the latest movement in a trace may be directly corrected or removed.

To correct an older movement:

```text
target old movement
→ remove every later movement, newest first
→ target becomes the latest movement
→ correct target
→ re-enter later real movements if they still need to exist
```

Correcting the latest Entrada recalculates that movement's discrepancy from the preceding trace state.

Corrections and removals remain auditable. The technical representation of that audit is a backend implementation choice; the functional rule is that later movements cannot remain active after an older movement that is being corrected.

## Repairers and configuration

Boquilhas configuration is edited through:

```text
Admin
→ App Definições
→ Boquilhas
```

This does not create a separate operational Definições destination in Boquilhas.

Repair routing is configured by production machine:

- B1;
- B2;
- B3;
- C1;
- C2;
- C3.

A Saída does not ask the operator to choose a repairer manually.

```text
BQ tool_id
→ associated machine
→ Boquilhas machine→repairer configuration
→ repairer used by the Saída
```

If the BQ is already in production, the production context confirms the machine. Before production, the BQ Tool's associated machine is used.

Changing a later machine→repairer configuration must not rewrite historical movements. The repairer that applied when a movement occurred remains part of that movement's historical truth.

## Consultation and artifacts

Boquilhas history is module-local and is composed from its durable repair traces and movement facts.

Job On must not duplicate Boquilhas repair history as another operational surface.

Boquilhas does not require PDF or email artifacts in the current scope.

## Rules that must not be violated

An implementation must not:

- create a new repair trace for every Saída;
- create more than one simultaneous unresolved pre-production trace for one BQ `tool_id`;
- ask for a second association confirmation after the BQ Tool was explicitly selected in Job On;
- keep the temporary direct `tool_id` as a competing anchor after a trace associates to `bq_id`;
- create a trace open/closed state;
- close or reset a trace merely because the machine changes production;
- make the machine card the only way to access Registo;
- require a BQ to be current on a machine before movements can be recorded;
- redirect late returns to the currently displayed BQ;
- move historical movements to a later production trace;
- mutate fixed Tool quantity because of repair movements;
- reject or truncate a physically observed exceptional Saída or Entrada merely to make arithmetic tidy;
- invent movements to reconcile quantities;
- treat `entrada_sem_reparacao` as a discrepancy type;
- use later normal movements to erase earlier discrepancy;
- treat Saldo as stock or a conventional running balance;
- display `0` in Saldo for ordinary movements;
- edit an older movement while later movements remain active after it;
- let later repairer configuration rewrite historical movement attribution;
- hardcode midnight, 07:00 or another global activation time for Boquilhas;
- delay a same-Job-On BQ context change until the next configured production-activation time;
- persist machine-card live values as independent mutable truth.
