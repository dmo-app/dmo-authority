# Identities

## Canonical Identities

- `tool_id`: The generic, canonical identity of any Tool (CM, MF, BQ). It is not owned by any specific module. A different lot is a different Tool.
- `jobon_id`: The production occurrence context. Created per production. Business key: `reference + production_number`.
- `cm_id`, `mf_id`, `bq_id`: Historical snapshots/contexts of a `tool_id` within a specific `jobon_id`. They are NOT new tools — they are photographs of the tool at that moment in that production.

## Rules

- Modules must enrich these IDs, never replace or duplicate them.
- The frontend must never mint canonical IDs.
- The frontend must never infer Tool identity from labels or displayed text.
- Internal canonical IDs are not the primary human-facing label.
