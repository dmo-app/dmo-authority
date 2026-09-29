# Job On — Selecionar Ferramentas

Job On normally selects Tools that are already registered in Ferramentas.

## Normal flow

For each required production Tool:

1. open the Ferramentas selection surface from Job On;
2. search, filter and inspect the Tools already registered;
3. explicitly select the intended Tool;
4. Ferramentas returns the canonical `tool_id` to Job On;
5. Job On creates the corresponding production context:
   - CM → `cm_id`;
   - MF → `mf_id`;
   - BQ → `bq_id`.

The normal path is therefore **reuse an existing registered Tool**.

## If the Tool does not exist

If the required Tool is not yet registered, the user may create it from the same Ferramentas flow.

The creation still belongs to Ferramentas:

```text
Job On
→ open Ferramentas
→ search / filter existing Tools
→ Tool not found
→ create new Tool in Ferramentas
→ new tool_id
→ return to Job On
→ select/use that tool_id
→ create cm_id / mf_id / bq_id for this production
```

Job On may initiate this flow, but it does not become a second Tool registry and does not create `tool_id` itself.

## Selection principle

Humans find and distinguish Tools through visible operational information such as:

- reference;
- lot;
- type;
- machine/line compatibility;
- other relevant display metadata.

The application resolves the explicit selection to `tool_id`.

The user's final choice is explicit. Filtering must not become silent auto-selection.

## Consultation inside the selection surface

The Ferramentas surface opened from Job On may also allow the user to inspect what is already registered before selecting or creating a Tool.

Consultation, filtering, selection and creation are different actions over the same Ferramentas registry.

## Production context

Once selected for a Job On, the Tool is represented in that production through the appropriate component context.

Those context IDs belong to the production occurrence. They do not replace the canonical Tool identity.

## Historical rule

A different lot is a different canonical Tool and therefore a different `tool_id`.

A later change to mutable Tool master facts must not rewrite the saved production context of an earlier Job On.
