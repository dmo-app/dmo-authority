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

4. Record production-specific configuration such as PU, CS, TP and other values that belong to this production rather than to the Tool master.

5. Continue into the downstream operational modules using the saved Job On context.

## Ownership

Job On owns the production occurrence and its selected component contexts.

It does not create replacement Tool identities.

Production-specific values must not be pushed into Ferramentas merely because they are used alongside a Tool.

## Human choice

Tool selection is explicit.

Filtering and relevance assistance may help the user find valid Tools, but the system must not silently choose the Tool that the user intended.
