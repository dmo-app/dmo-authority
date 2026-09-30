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

## Tool candidate discovery

Job On uses known production information to reduce the Tool candidate set before explicit selection.

Candidate discovery follows three conceptual levels:

```text
1. REFERENCE ASSISTANCE
   - direct canonical-reference match
   - OR explicit MF-reference association where applicable

2. COMPATIBILITY FILTERING
   - machine / line compatibility

3. TOOL DISTINCTION
   - lot
   - other visible Tool facts needed to identify the candidate

→ candidate Tool(s)
→ explicit human selection
→ canonical tool_id
```

For CM, a direct reference match is the normal path. An optional MF-reference association exists only for exceptional cases where the CM's canonical reference differs from the relevant MF/production reference.

Example:

```text
production / MF reference = 5810

CM 5810
→ candidate through direct canonical-reference match

CM 5809
→ may also be a candidate when CM 5809 has an explicit MF-reference association to 5810
```

If CM 5809 is selected, Job On still displays and stores the selected Tool as CM reference 5809. The MF-reference association never becomes a replacement CM reference.

## Selection principle

Humans find and distinguish Tools through visible operational information such as reference, lot, type, machine/line compatibility and other relevant display metadata.

These values are candidate-discovery attributes. They do not form a derived Tool identity key.

```text
filters reduce candidates
!=
filters determine identity
```

Even if filtering produces exactly one candidate:

```text
one candidate
→ user explicitly selects it
→ existing canonical tool_id is accepted
```

Never:

```text
reference + machine + lot
→ calculate / infer tool_id
```

The application resolves the user's explicit selection to the persisted canonical `tool_id`.

## Consultation inside the selection surface

The Ferramentas surface opened from Job On may also allow the user to inspect what is already registered before selecting or creating a Tool.

Consultation, filtering, selection and creation are different actions over the same Ferramentas registry.

## Production context

Once selected for a Job On, the Tool is represented in that production through the appropriate component context.

Those context IDs belong to the production occurrence. They do not replace the canonical Tool identity.

## Historical rule

A different lot is a different canonical Tool and therefore a different `tool_id`.

Therefore, changing the selected Tool because the lot changed is a Tool replacement, not an edit of the existing Tool identity.

Example for CM inside the same Job On:

```text
old cm_id
→ tool_id A
→ lot 001

operator selects the Tool for lot 002

tool_id B
→ lot 002

new cm_id
→ tool_id B

old cm_id
→ remains historical
→ still references tool_id A / lot 001
```

The implementation must not mutate `tool_id A` from lot 001 to lot 002, and it must not retarget the old `cm_id` from `tool_id A` to `tool_id B`.

The same identity-preservation rule applies to MF and BQ production contexts.

A later change to mutable Tool master facts must not rewrite the saved production context of an earlier Job On.
