# Admin — Backend Contract

This file contains Admin-specific backend/setup details that do not belong in the functional `ADMIN.md`.

## Authentication authority

The external authentication provider owns authentication identities and credentials.

DMO stores/uses only the application-side association and access facts required by the product.

DMO must never become a second password authority or persist definitive user passwords.

## ADMIN setup / provider connection

Initial setup is responsible for connecting and validating the required provider/infrastructure configuration and associating an existing provider user as DMO ADMIN.

The intended flow is conceptually:

```text
open DMO setup
→ connect/authenticate to provider management surface where required
→ choose/identify the target project
→ retrieve or enter the runtime configuration required by DMO
→ validate connectivity/configuration
→ select/confirm an existing provider Auth user
→ associate that identity as DMO ADMIN
→ finish setup
```

Setup must not silently create a new provider Auth user merely to satisfy DMO ADMIN association.

Provider-management authorization used during setup must not become a permanent runtime requirement unless the final provider integration explicitly requires it.

## Technical choices that require provider validation

Implementation may choose the concrete provider-management flow, but must validate rather than guess:

- which provider OAuth/management flow is available;
- which project/runtime configuration can be retrieved programmatically;
- which runtime credentials/configuration DMO actually requires;
- which database/provider credentials are required by the final architecture;
- how sensitive configuration is persisted securely;
- how temporary provider-management authorization is revoked/disconnected after setup where applicable;
- how ADMIN reassociation/recovery works when the external identity or project connection changes.

These are technical integration choices, not alternative product identity models.

## Normal User provisioning

Where DMO provisions a provider authentication identity for a normal operational User:

- the application User remains identified operationally by the DMO user facts defined in `../ADMIN.md`;
- initial credentials may be temporary;
- the user must establish/change the definitive provider password according to the provider flow;
- DMO must not retain that definitive password.

## Access resolution

Backend authorization resolves current effective access through the persisted association:

```text
User
→ current Template or none
→ assigned module/capability permissions
→ requested operation
```

Template names, UI labels, hidden buttons, URLs or historical role text never substitute for this authorization check.

The backend must authorize the requested operation even if the frontend already hid/shown the corresponding UI action.

## Historical attribution

Authentication/account changes must not break attribution of previously persisted operational actions.

Historical records must remain attributable even if the user later becomes stand-by, changes Template, loses access or the provider account is disabled/removed.
