---
title: Security
has_children: false
nav_order: 2
---

# Anko Investor Security

## Login Security

Anko Investor uses [Auth0](https://auth0.com) to handle signup and logins. We receive, and store, from Auth0:

* Email adresses- this allows us to handle things like allowing users to login via many services, and to allow for inviting users to organisations
* Nickname - this is used for reporting user operations in the Notifications panel in the webapp

At no point do we store passwords, whether in a hashed, salted+hashed, plaintext, or any other form.

We enforce MFA on all logins via Auth0.

## Forecast Stream Security

Anko Investor generates a token following the scheme:

```golang
fmt.Sprintf("anko-%s", hex.EncodeToString(uuid.Must(uuid.NewV4()).Bytes()))
```

which is used to authorise access to forecast streams. This format can be used when scanning source code for key leaks.
