# Scope

Este documento define os limites funcionais do Beta DMO.

# IN SCOPE (Beta Product)

- Identidades e Acessos (Admin, Users, Templates).
- Catálogo de Ferramentas (`tool_id`).
- Job On Light (planeamento de produção e contextos CM/MF/BQ).
- Controlo Create (superfície de criação/edição):
  - Peso (criação, medição, cálculo, submissão).
  - Comparação (workflow dentro do Peso Create).
  - **Pegamentos** (medição de componentes CM/MF/BQ).
  - Folha (`controlo_sheet_id`).
  - Resumo (`resumo_id`).
  - Definições operacionais (Reparadores, Diretório PDF, Listas/Templates de email).
- Controlo Approve (revisão, decisão e histórico de Pesos submetidos).
- Boquilhas (agregado de reparação externa e movimentos).

# OUT OF SCOPE (Strictly Forbidden to design or code in Beta)

- Armazém (posições físicas, stock, logística).
- Reparação Interna / Programada (fluxos complexos de oficina).
- Tampões (como módulo independente).
- Ciclo de vida completo de Ferramentas (verificação, reset, desativação).
- Módulo de História (como entidade separada).
- Full Job On lifecycle (revision workflow, verification catalogue, manual-family sheets).

# Implementation Status Matrix

| Module / Surface | Beta Scope | Backend Status | Frontend Status |
|---|---|---|---|
| Admin / Access | IN | Implemented | Implemented |
| Tools (tool_id) | IN | Implemented | Contextual picker |
| Job On Light | IN | Implemented | Committed, not yet navigable |
| **Controlo Create** | IN | Partial | Committed, not yet navigable |
| ↳ Peso (core) | IN | Implemented | Committed |
| ↳ Comparação | IN | NOT IMPLEMENTED | Target only |
| ↳ **Pegamentos** | IN | **NOT IMPLEMENTED** | **Local working prototype** (see RULES.md) |
| ↳ Folha | IN | NOT IMPLEMENTED | Target only |
| ↳ Resumo | IN | Partial | Target only |
| ↳ Definições | IN | Implemented | Committed |
| Controlo Approve | IN | NOT IMPLEMENTED | Target only |
| Boquilhas | IN | NOT IMPLEMENTED | Target only |