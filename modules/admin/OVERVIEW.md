# Admin — Overview

This file is authoritative for the DMO Admin identity boundary and administration surface.

## 1. ADMIN is a DMO role over an external Auth identity

Supabase Auth is the authority for authentication identity and credentials.

DMO is the authority for what an authenticated identity is allowed to do inside the application.

Therefore:

```text
Supabase Auth user
→ existing external authentication identity

DMO
→ associates one existing Auth identity as ADMIN
```

ADMIN is not a special Supabase user type. In Supabase Auth it remains a normal Auth user.

The administrative distinction exists in DMO.

## 2. DMO never creates the Auth identity used as ADMIN

The identity that becomes ADMIN must already have been created outside DMO through Supabase administration.

No DMO page, endpoint, seed, bootstrap, migration, recovery flow or convenience action may create the Supabase Auth user that will become ADMIN.

DMO may only:

1. connect to the configured Supabase/Auth infrastructure;
2. validate that the selected Auth user already exists;
3. associate that existing Auth identity as the DMO ADMIN.

This separation is a security boundary.

The application must not be able to manufacture the authentication identity that grants administration over itself.

## 3. ADMIN is not "the first user"

DMO must never assign ADMIN because:

- a user is the first person to log in;
- the user table is empty;
- no current ADMIN row exists;
- the application has just started;
- a migration or seed is running.

Technical database state must not silently choose a privileged human identity.

## 4. ADMIN surface

ADMIN is not a USER with every operational module enabled.

ADMIN enters the administration/configuration surface of DMO.

Operational access for normal USERS follows the DMO access model (USER → Template → modules/permissions).

The ADMIN association must not be implemented by granting every operational module to a normal USER.

## 5. External authority remains external

Credential creation, password management and creation of the Auth identity used as ADMIN remain outside DMO in Supabase Auth administration.

DMO stores/uses only the association required to recognize which already-existing Auth identity is its ADMIN.

See `SETUP.md` for installation and reassociation behavior.
