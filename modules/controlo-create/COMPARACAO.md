# Controlo Create — Comparação

Comparação belongs entirely to Controlo Create.

There is no separate Comparação approval workflow.

## Flow

1. Start a Comparação against a decided Peso.
2. Add or reuse the relevant `cm_id`.
3. Measure using the facts frozen by the referenced Peso.
4. Decide per CM:
   - `Manter`;
   - `Colocar de parte`.
5. Confirm the Comparação.

`Colocar de parte` requires a non-empty justification.

## Identity and relations

- Comparação has its own `comparacao_id`.
- It references the existing `peso_id`.
- It reuses `cm_id`; it does not create a new CM.
- It does not modify the original Peso.
- It does not create `previous_peso_id`.
- Multiple Comparação events may exist for the same Peso.

Warnings or measurement results do not silently choose the human decision.
