# DMO

Current DMO source of truth, organized by module.

The repository is deliberately small: a rule lives in the module that owns it, and duplicate historical/recovery copies are not kept beside the current rule.

## Structure

```text
modules/
├── global/
│   ├── GLOBAL.md
│   ├── backend/
│   │   └── BACKEND.md
│   └── frontend/
│       └── FRONTEND.md
├── admin/
│   ├── ADMIN.md
│   └── backend/
│       └── BACKEND.md
├── ferramentas/
│   └── FERRAMENTAS.md
├── job-on/
│   └── JOB_ON.md
├── controlo/
│   ├── CONTROLO.md
│   └── backend/
│       ├── DOCUMENTS.md
│       └── PESO_HISTORICAL_DIFFERENCE.md
└── boquilhas/
    └── BOQUILHAS.md
```

## Rule

- Functional truth lives once in the owning module document.
- `backend/` adds backend-specific contracts without redefining the functional rule.
- `frontend/` adds frontend-specific contracts without redefining the functional rule.
- Cross-module rules live under `modules/global/`.
- Old implementations, reports, backups and prototypes may be evidence during investigation but do not override the current module source.
- Git history is the history. Do not keep stale duplicate rules in the active tree merely to preserve how the project evolved.
