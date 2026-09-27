# Rules

## Rule 1: Frontend without Backend (Local Prototypes)

Some modules (like Pegamentos) currently exist as local HTML prototypes used in real work, but have NO backend implemented yet.

- These are classified as "Working Prototypes".
- Data must live exclusively in the browser session (`sessionStorage`).
- It is strictly FORBIDDEN to invent REST endpoints, create database schemas, or mock APIs for these prototypes until the backend phase officially begins.
- The prototype serves to validate UI/flow, not to define persistence.
- When the backend is implemented, it follows the rules in this repo, NOT the structure of the HTML prototype.

## Rule 2: Explicit Human Choice

The system must never silently infer industrial decisions. If there are multiple valid Tools, Job Ons, or previous Pesos, the UI must force the user to click and select one.

## Rule 3: Warnings are not Decisions

Warnings must be displayed to the user. The frontend must never automatically approve, reject, block, or clamp data based on a warning unless this repo explicitly demands blocking validation.

## Rule 4: Presentation is not Domain Authority

The frontend may render, collect input, preserve local draft state and orchestrate published actions.

It must not:
- Mint canonical IDs.
- Invent persistence relationships.
- Implement backend-owned formulas as a second authority.
- Turn warnings into industrial decisions.
- Synthesize actor/time/audit facts.
- Infer permissions from role/profile labels.

## Rule 5: Unsaved State Preservation

When a flow temporarily leaves a form to select/create a Tool or repair missing context:

- preserve entered values;
- preserve row identities;
- cancel returns unchanged;
- successful return applies only the explicitly selected/created result.
