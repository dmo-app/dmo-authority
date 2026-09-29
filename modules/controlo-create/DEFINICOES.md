# Controlo Create — Definições

Controlo Definições belongs to Controlo Create.

It is not:
- a separate capability;
- an Admin surface;
- a permission named `controlo.criar.definicoes`.

Access follows the Controlo Create module/capability boundary.

## Current settings

The settings area includes at least:

- base directory for generated PDFs;
- glass density by process where applicable;
- email templates;
- production people/email configuration;
- Line B email list;
- Line C email list;
- fast sending of Peso/PDF artifacts.

For production email routing:
- B1/B2/B3 resolve to the B recipient configuration;
- C1/C2/C3 resolve to the C recipient configuration;
- recipients must not be hardcoded into the sending workflow.

Settings provide configuration consumed by operational workflows; they do not become a second authority for records created by those workflows.
