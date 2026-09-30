# Boquilhas — Frontend Contract

This file defines the stack-independent interface contract for Boquilhas.

It must be read together with the functional sources below. Those files own business meaning; this file makes each user interaction precise enough for design and frontend implementation.

## Functional sources

- `../OVERVIEW.md`
- `../REGISTO.md`
- `../MOVIMENTOS.md`
- `../HISTORICO.md`
- `../DEFINICOES.md`

## Contract boundary

Use the structure in `../../../TECHNICAL_CONTRACT_STANDARD.md`.

For each Boquilhas page/function/interaction, specify:

- user goal;
- entry context and stable identity;
- exact read model needed;
- displayed live/historical/derived facts;
- user-entered or explicitly selected facts;
- local/draft state;
- minimal backend request;
- facts that must not be resent as authority;
- loading/ready/error/conflict/pending/success states as applicable;
- navigation and return behavior;
- permission/capability behavior;
- design handoff to `dmo-app/dmo-design`.

The frontend carries operation context but does not become a duplicate domain store. Visible labels are not identity, and backend-owned facts/calculations are not re-created client-side as truth.

If this work exposes an unresolved functional rule, resolve it in the owning functional document before defining the interface behavior.

## Implementation independence

This contract does not describe whether a page/component already exists. It also does not prescribe a frontend framework, component tree, hook/state library, CSS structure or route implementation unless such a detail is explicitly required by the blueprint.
