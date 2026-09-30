# Boquilhas — Backend Contract

This file defines the stack-independent server/application contract for Boquilhas.

It must be read together with the functional sources below. Those files own business meaning; this file makes that behavior precise enough for backend implementation and integration.

## Functional sources

- `../OVERVIEW.md`
- `../REGISTO.md`
- `../MOVIMENTOS.md`
- `../HISTORICO.md`
- `../DEFINICOES.md`

## Contract boundary

Use the structure in `../../../TECHNICAL_CONTRACT_STANDARD.md`.

For each Boquilhas operation, specify:

- intent;
- nearest truthful anchor;
- client-supplied business facts;
- context resolved by the backend;
- backend-created IDs/actor/timestamps;
- minimum reads;
- durable writes;
- concurrency contract when required;
- purpose-specific result/read model;
- defined refusals;
- cross-module contracts consumed.

The request should carry the minimum truthful anchor + intent + new facts. Server-resolvable context is resolved server-side rather than copied back from the client as authority.

If this work exposes an unresolved functional rule, resolve it in the owning functional document before defining the backend behavior.

## Implementation independence

This contract does not describe whether code already exists. It also does not prescribe concrete classes, framework components, ORM entities, migrations, route organization or database layout unless such a detail is explicitly required by the blueprint.
