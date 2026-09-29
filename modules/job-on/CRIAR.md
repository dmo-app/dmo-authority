# Job On — Criar

Job On Create creates a new production occurrence.

## Flow

1. Identify the production:
   - reference;
   - production number;
   - machine;
   - production date.

2. Select the relevant CM, MF and BQ Tools from Ferramentas.

3. Create the production context:
   - new `jobon_id`;
   - production-specific `cm_id`, `mf_id` and `bq_id` as applicable;
   - each component context references the selected canonical `tool_id`.

4. Record production-specific configuration such as PU, CS, TP/Tampão and other values that belong to this production rather than to the Tool master.

5. Continue into the downstream operational modules using the saved Job On context.

## TP / Tampão / Calote

The applicable TP/Tampão/Calote value is defined as part of preparing the production in Job On.

This is a production-specific decision. The person preparing the Job On may choose the applicable calote according to the production setup and the expected/observed glass distribution needed to balance the production.

The value therefore belongs to the Job On production context and is reachable through `jobon_id`.

Peso consumes this production value when performing its own calculation/technical evaluation and preserves the value it actually used with the Peso record.

That consumption does not transfer ownership of the production configuration to Peso, and it does not require duplicating the production TP/Calote into `controlo_id`.

No canonical physical `tampao_id` is introduced by this rule. The physical piece may be reused across different references/lots, but DMO currently preserves the production value needed by the workflow rather than inventing a separate Tool identity for Tampão.

## Ownership

Job On owns the production occurrence and its selected component contexts.

It does not create replacement Tool identities.

Production-specific values must not be pushed into Ferramentas merely because they are used alongside a Tool.

## Human choice

Tool selection is explicit.

Filtering and relevance assistance may help the user find valid Tools, but the system must not silently choose the Tool that the user intended.
