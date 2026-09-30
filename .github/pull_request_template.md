## Authority change

### What changed?
<!-- Describe only the authority changed by this PR. -->

### Why?
<!-- Why is this change needed now? -->

### Support / evidence
<!-- Owner decision, current verified workflow, or current implementation evidence. -->

### What does this NOT imply?
<!-- State the boundaries so the change cannot be over-generalized later. -->

## Drift check

- [ ] No legacy role labels (`Operador` / `Responsável`) were introduced as authorization authority.
- [ ] No new identity or FK was introduced merely for UI/query convenience.
- [ ] Different Tool lot still means a different canonical `tool_id`.
- [ ] `resumo_id` was not reintroduced.
- [ ] Read-model composition was not promoted into persisted domain structure.
- [ ] Any changed canonical rule is intentional and owner-reviewed.
