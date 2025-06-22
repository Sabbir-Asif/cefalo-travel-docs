# Understanding OAuth 2.0: Delegated Authorization in the Modern Web

As applications integrate more deeply with third-party services — like Google, GitHub, or Facebook — there's a growing need for **secure, delegated authorization**. OAuth 2.0 has become the industry standard for solving this problem.

In this blog, we’ll cover:

* What OAuth 2.0 is and how it evolved from OAuth 1.0
* The core flows in OAuth 2.0
* How OAuth 2.0 works under the hood
* Security considerations and best practices

---

## What Is OAuth 2.0?

**OAuth 2.0** is an **authorization framework** — not an authentication protocol.

It allows a user to grant limited access to their resources on one service (like Google) to another service (like your app) **without sharing credentials**.

For example:

> You want to let users sign in to your app using their Google account.
> OAuth 2.0 allows your app to request access to the user's profile, without ever seeing their Google password.

---

## Was There an OAuth 1.0?

Yes. OAuth 1.0 was the first version of the protocol, but it was:

* Complex to implement (used cryptographic signatures for every request)
* Difficult to debug
* Lacked flexibility for mobile and browser-based apps

OAuth 2.0 was created to simplify the developer experience, support more use cases (SPAs, mobile, etc.), and be extensible.

---

## OAuth 2.0 Roles

* **Resource Owner**: The user (you).
* **Client**: The application requesting access on behalf of the user.
* **Authorization Server**: The service that authenticates the user and issues tokens (e.g., Google, GitHub).
* **Resource Server**: The API that serves the protected user data (e.g., Google Profile API).

---

## OAuth 2.0 Grant Types (Flows)

OAuth 2.0 defines several **authorization flows** depending on the app type.

### 1. Authorization Code Flow (Recommended for Web Apps)

Used by server-side applications that can safely store secrets.

Steps:

1. User is redirected to the provider (e.g., Google).
2. After login, the provider redirects back with an **authorization code**.
3. Your backend exchanges this code for an **access token**.
4. You can now access user data from the provider.

### 2. Authorization Code Flow with PKCE (for SPAs and mobile apps)

Same as above but uses a **code challenge** and **code verifier** to protect against code interception.

### 3. Client Credentials Flow

Used for machine-to-machine authentication (no user involved).

### 4. Implicit Flow (Deprecated)

Old approach used by SPAs that couldn't securely store secrets. Now replaced by PKCE.

---

## What Does an OAuth 2.0 Token Look Like?

When the authorization server grants access, it returns:

```json
{
  "access_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
  "token_type": "Bearer",
  "expires_in": 3600,
  "scope": "email profile",
  "id_token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

* `access_token`: Used to access the user’s data from the resource server.
* `id_token` (optional): JWT that contains authenticated user identity (used in OpenID Connect).
* `scope`: Specifies which data your app can access.

---

## OAuth 2.0 vs OpenID Connect (OIDC)

* **OAuth 2.0** = Authorization
* **OpenID Connect (OIDC)** = Authentication built on top of OAuth 2.0

If you want to let users **log in with Google**, you’re actually using OAuth 2.0 + OpenID Connect.

---

## How OAuth 2.0 Ensures Security

### 1. Authorization Codes Are Short-Lived

They are valid only for a few minutes and can only be used once.

### 2. Redirect URIs Must Match

Apps must register their redirect URIs, and the provider enforces an exact match.

### 3. Client IDs and Secrets

Used to identify and verify legitimate apps.

### 4. HTTPS Is Mandatory

OAuth flows must use HTTPS to prevent interception of tokens or codes.

### 5. PKCE (Proof Key for Code Exchange)

Adds an extra layer of protection for SPAs and mobile apps by preventing code injection.

---

## Where OAuth 2.0 Fits Into Your JWT System

After successful OAuth 2.0 login (e.g., with Google), your backend:

* Verifies the OAuth ID token (JWT)
* Looks up or creates the user in your database
* Issues **your own access and refresh tokens** to the user

This means you maintain full control over your session system using JWT, while OAuth handles identity federation.

---

## Summary

OAuth 2.0 is a robust and flexible framework that powers modern identity systems. It allows apps to:

* Securely access user data on third-party services
* Offer seamless sign-in experiences
* Protect user credentials

When combined with JWT and OpenID Connect, OAuth 2.0 provides a secure and scalable solution for both authentication and authorization in modern web applications.

