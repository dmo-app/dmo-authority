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
- production recipient/person configuration;
- Line B recipient configuration;
- Line C recipient configuration;
- fast sending of Peso/PDF artifacts.

## PDF recipients

Controlo Create → Definições owns the operational configuration used to resolve PDF recipients.

These configured recipients are production/email recipients. They must not be treated as application-authentication users merely because a person may also have an application account.

Recipients may be associated with the relevant machine/line group.

Current routing:

- B1/B2/B3 resolve to the B recipient configuration;
- C1/C2/C3 resolve to the C recipient configuration.

When a PDF is sent, the workflow resolves the applicable group and its configured recipients.

The person sending the PDF does not need to select an arbitrary recipient list on every send.

Recipient email addresses must not be hardcoded into the sending workflow.

This configuration belongs to Controlo Create, not Admin, because it configures the operational document-sending workflow rather than application authentication or access permissions.

Settings provide configuration consumed by operational workflows; they do not become a second authority for records created by those workflows.
