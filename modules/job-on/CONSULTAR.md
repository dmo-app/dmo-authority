# Job On — Consultar

Job On View is the consultation surface for saved production occurrences.

It allows the user to inspect the production facts and the Tool contexts attached to that production without editing them.

## Expected context

A saved Job On can expose, as applicable:

- reference;
- production number;
- machine;
- production date;
- selected CM / MF / BQ contexts;
- production-specific configuration;
- links or navigation into downstream module information for that same production.

The page must resolve the saved production by `jobon_id`.

Visible fields such as reference or production number help humans find the record; they do not replace the stable production identity.

## Boundary

Consultation must not mutate the Job On.

Job On View does not gain edit behavior through a hidden UI state or historical role mapping.
