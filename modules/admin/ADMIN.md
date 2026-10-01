# Admin

Admin manages DMO users, access templates and application settings. It is an administration surface, not an operational production role.

This file is the single functional source for the Admin module.

## Admin identity and login

ADMIN is a separate application-administration identity.

The authentication provider owns authentication identities. DMO associates an existing provider identity as ADMIN; DMO does not create a special local ADMIN username/password authority.

ADMIN authenticates using the provider email-based identity and enters the administration/configuration surface.

ADMIN does not gain operational production access merely because it is ADMIN.

## Main surfaces

Admin exposes three administration areas:

```text
Users
Templates
App Definições
```

There is no standalone Modules administration tab.

### Users

A DMO User has the operational identity/details required by the application, including:

- name;
- operator / BA Glass identifier, maximum 4 digits;
- current Access Template or none;
- availability state: active or stand-by.

Leading zeros in the operator / BA Glass identifier are meaningful and must be preserved.

A User does not carry a second access authority such as a legacy title/role. Access comes from the current Template association.

One User has at most one current Access Template.

```text
User
→ zero or one current Template
→ modules / capabilities / permissions
```

The same User ↔ Template relation may be managed from either Users or Templates. Those are two administration views over one relation, not duplicated ownership.

Normal users authenticate through the operational login using their operator / BA Glass identifier. Their provider authentication identity may be provisioned by DMO as part of account creation where supported.

If initial credentials are provisioned, they are temporary and the definitive password remains owned by the authentication provider. DMO must never store user passwords as application data.

Stand-by preserves the User and historical attribution while disabling operational access.

If a User is removed from active administration, historical records must still remain attributable to the person who performed them. Deleting an account must never erase or rewrite historical actor truth.

### Templates

An Access Template is a named collection of application access assignments.

```text
Template
→ modules / capabilities / permissions

User
→ current Template
→ effective access
```

Template names are human-readable labels only. A label does not grant access by itself and must never be interpreted as a hardcoded role.

Access is determined by the permissions/capabilities assigned to the Template.

A Template may be associated with many Users. A User has at most one current Template.

Changing a Template changes the effective access of Users currently assigned to it. It does not rewrite historical actor/action records.

Templates may be created, edited and assigned through Admin.

### App Definições

App Definições centralizes editing of infrequently changed, sensitive or administrative settings owned by application modules.

```text
Admin
→ App Definições
→ select module
→ edit that module's settings
```

Centralizing the editing surface does not transfer ownership of the setting to Admin.

Examples:

```text
Controlo settings
→ owned by Controlo
→ edited through Admin → App Definições → Controlo

Boquilhas settings
→ owned by Boquilhas
→ edited through Admin → App Definições → Boquilhas
```

Changing current configuration must never retroactively rewrite historical operational facts that consumed an earlier value.

## Initial setup

A blank/unconfigured DMO installation may expose a setup surface before an ADMIN is associated.

Setup exists to connect/validate the required provider/infrastructure configuration and associate an existing authentication-provider user as ADMIN.

Setup must not create a second local authentication authority.

The same principle applies if the ADMIN association must later be recovered after the external provider identity/configuration changed: recovery restores the association; it does not invent a parallel DMO credential system.

## Access rules

The effective operational access model is:

```text
User
→ current Access Template
→ assigned modules / capabilities / permissions
→ backend-authorized operation
```

UI labels, hidden/visible buttons, URLs, template names or historical job titles are not authorization authorities.

Create/View/Approve or similar capabilities are granted according to the owning module's definitions. A Template may combine the capabilities required by one User.

## Historical integrity

Administration changes affect current access/configuration. They do not rewrite past operational truth.

In particular:

- changing a User Template does not rewrite old actions;
- putting a User on stand-by does not erase history;
- changing module configuration does not rewrite records that consumed older values;
- changing a human-readable Template name does not change the permissions unless the actual assigned permissions are changed;
- deleting or disabling an authentication identity must not destroy application historical attribution.

## Rules that must not be violated

An implementation must not:

- use a Template name or legacy role label as an authorization rule;
- give ADMIN implicit operational permissions merely because it is ADMIN;
- require normal operational users to be administered as email-based ADMIN identities;
- store definitive user passwords in DMO;
- create a second independent User↔Template relation for the Users and Templates screens;
- require every User to have a Template if the valid state is no Template;
- erase historical actor attribution when a User becomes inactive, stand-by or removed;
- treat App Definições as ownership transfer from the operational module to Admin;
- let a current configuration edit retroactively rewrite historical operational facts.
