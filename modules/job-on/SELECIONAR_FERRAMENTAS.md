# Job On — Selecionar Ferramentas

Job On selects existing canonical Tools from Ferramentas.

## Selection principle

Humans find and distinguish Tools through visible operational information such as:

- reference;
- lot;
- type;
- machine/line compatibility;
- other relevant display metadata.

The application resolves the explicit selection to `tool_id`.

The user's final choice is explicit. Filtering must not become silent auto-selection.

## Production context

Once selected for a Job On, the Tool is represented in that production through the appropriate component context:

- CM → `cm_id`;
- MF → `mf_id`;
- BQ → `bq_id`.

Those context IDs belong to the production occurrence. They do not replace the canonical Tool identity.

## Historical rule

A different lot is a different canonical Tool and therefore a different `tool_id`.

A later change to mutable Tool master facts must not rewrite the saved production context of an earlier Job On.
