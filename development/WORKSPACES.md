# Active development workspaces

This note records the current DMO development locations. It is operational development context, not domain authority.

## Functional source

- `dmo-app/dmo-recovery` — current functional source for DMO rules, identities, flows, and product decisions used by implementation work.
- `dmo-app/dmo-authority` is legacy and must not be used as the current authority source.

## Implementation workspaces

- `D:\AI Dev\DMO\Alpha` — current main Alpha implementation workspace, including backend/integration work.
- `D:\AI Dev\codex\Alpha` — current frontend Alpha workspace being developed by Codex.

The two implementation workspaces must not be treated as competing functional authorities. When implementation behavior requires product/domain clarification, consult the current `dmo-app/dmo-recovery` source.

The frontend workspace is intended to be published to its own GitHub repository so its development state is preserved independently from the main Alpha workspace.
