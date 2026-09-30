# Admin — Users

This file defines the normal DMO User administration surface.

A normal USER is distinct from the special DMO ADMIN association described in `OVERVIEW.md` and `SETUP.md`.

## 1. User list

The Admin → Users list must expose the operational identity information needed to identify and manage a User.

The current User facts are:

- name;
- operator number / BA Glass identification;
- login email;
- current Access Template, if assigned;
- current account availability such as active or stand-by.

The operator number / BA Glass identification is at most four digits.

It is an identifier, not a quantity. Its representation must preserve the entered identification faithfully, including any meaningful leading zero.

## 2. User detail

Opening a User shows the same canonical User record and its current Template association.

Conceptually:

```text
User
├── Name
├── Operator / BA Glass ID
├── Login email
├── Template
└── Account availability
```

The User detail must not maintain a second role/profile label independent from the Template.

DMO has no separate editable User title/profile field acting as a second access source beside the Access Template.

Human-facing profile/access labels such as `Operador`, `Reparador` or `Chefe` come from the name of the associated Access Template. The label itself is not an authorization rule.

## 3. User ↔ Template association

The current access model uses one current Access Template per User, or no Template.

```text
User
→ Template A

or

User
→ no Template
```

A User without an Access Template may remain registered but has no operational module access derived from a Template.

From the User detail, ADMIN may:

- assign the User to a Template;
- change the User to another Template;
- remove the User from its current Template.

This is the same canonical association managed from the Template detail page.

Changing it from Users or Templates must not create two independent relations.

## 4. Password reset

The User detail exposes a password-reset action for the User's login.

The authentication provider remains responsible for credentials and password-reset mechanics.

DMO:

- may initiate the supported provider reset flow;
- must not expose an existing password;
- must not store a replacement password as application-owned user data;
- must not treat a password reset as a change of User identity or Template.

## 5. Stand-by

A User may be placed in **stand-by**.

Stand-by means the User record and historical attribution remain preserved while normal operational access is disabled.

It is not deletion.

The User may later be returned to active use without recreating historical operational records.

Stand-by must not rewrite operations, approvals, movements or other historical facts previously attributed to that User.

## 6. Delete

If a person has definitively left and the User no longer needs to remain available for operational use, Admin may expose a delete action.

Deletion must never destroy or orphan historical attribution for actions already performed by that User.

The exact persistence strategy may preserve a minimal historical identity/tombstone or another technically truthful representation, but the product invariant is:

```text
User removed from active administration
!=
historical actions lose their actor
```

Deletion is distinct from temporary stand-by and must not happen automatically merely because a User is placed in stand-by.

## 7. Explicitly forbidden interpretations

An implementation must not:

- use a separate User title/profile field as a second access/profile authority beside the Template;
- derive permissions by comparing labels such as `Operador`, `Reparador` or `Chefe`;
- allow a stand-by User to retain normal operational access;
- delete historical records when a User is put in stand-by or removed;
- maintain separate User→Template and Template→User assignment systems;
- treat the operator/BA Glass identification as an arithmetic quantity.
