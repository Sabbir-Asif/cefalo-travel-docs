# Understanding JWT and Why It's Used in Modern Web Authentication

In the world of modern web development, user authentication is one of the most important concerns. As applications become more decoupled and distributed — with mobile clients, SPAs (Single Page Applications), and microservices — the need for a scalable and stateless authentication mechanism becomes crucial.

This is where **JWT (JSON Web Token)** comes in.

In this blog, we'll explore what JWT is, why it's widely used, how it works, and how to use it effectively in real-world applications.

---

## What is JWT?

**JWT** stands for **JSON Web Token**. It is an open standard (RFC 7519) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information is **digitally signed**, which means the receiver can verify that the data has not been tampered with.

JWTs are typically used for:

* User authentication
* Authorization (role-based access control)
* Secure communication between services

---

## Why JWT Instead of Other Methods?

Traditional web authentication relied heavily on **session-based authentication**, where:

* The server stores session data in memory or a database.
* A session ID is stored in a browser cookie.

While effective for monolithic apps, this model doesn't scale well in distributed systems. JWT solves this by being:

* **Stateless**: No server memory is needed to keep track of sessions.
* **Self-contained**: All the necessary information is stored in the token itself.
* **Portable**: Ideal for APIs and microservices.

---

## The Structure of a JWT

A JWT is composed of **three parts**:

```
xxxxx.yyyyy.zzzzz
```

These parts are:

1. **Header** – metadata about the token
2. **Payload** – the claims (data)
3. **Signature** – used to verify the token’s integrity

All three parts are base64url-encoded and separated by dots (`.`).

---

### 1. Header

The header typically consists of two fields:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

* `alg`: Signing algorithm (e.g., HS256, RS256)
* `typ`: Token type, usually `"JWT"`

---

### 2. Payload

The payload contains the **claims**, which are statements about an entity (usually the user), and additional data.

Example:

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "iat": 1718986800,
  "exp": 1718990400
}
```

Some standard (registered) claims include:

* `iss`: Issuer
* `sub`: Subject
* `aud`: Audience
* `exp`: Expiration time
* `nbf`: Not before
* `iat`: Issued at
* `jti`: JWT ID (unique identifier for the token)

You can also include custom claims like `role`, `email`, etc.

> **Note**: The payload is **not encrypted**. Anyone with the token can read it. Sensitive information should never be placed in the payload.

---

### 3. Signature

The signature is used to verify that the token hasn’t been altered.

If you're using a symmetric algorithm like `HS256`, the signature is created like this:

```
HMACSHA256(
  base64urlEncode(header) + "." + base64urlEncode(payload),
  secret
)
```

If you're using an asymmetric algorithm like `RS256`, it uses a private key to sign and a public key to verify.

The signature ensures that:

* The token was created by a trusted party.
* The token has not been modified.

---

## How JWT Signature Verification Works

When a client sends a token, the server:

1. Decodes the token.
2. Reconstructs the signature using the header + payload and the known secret or public key.
3. Compares the reconstructed signature with the signature part of the token.

If they match, the token is considered valid.

---

## Popular JWT Functions in Node.js

The most commonly used library in Node.js for JWTs is `jsonwebtoken`.

### `jwt.sign(payload, secretOrPrivateKey, options)`

Creates a new token:

```ts
const token = jwt.sign({ userId: 123 }, privateKey, {
  algorithm: "RS256",
  expiresIn: "15m",
});
```

Use this when:

* Creating a token after successful login
* Signing system-generated tokens (like email verifications)

---

### `jwt.verify(token, secretOrPublicKey, options)`

Verifies and decodes the token:

```ts
const decoded = jwt.verify(token, publicKey);
```

Use this when:

* Validating tokens in middleware before granting access to protected routes

---

### `jwt.decode(token, options)`

Decodes the token **without verifying** the signature:

```ts
const decoded = jwt.decode(token);
```

Use this when:

* You want to extract data from the token without checking authenticity (e.g., for debugging)
* Not recommended for secure operations

---

## Summary

JWT is an essential tool in modern authentication systems:

* It enables stateless and scalable APIs.
* It securely carries identity and claims across services.
* It reduces server-side session storage requirements.

In the next blog, we’ll explore the **flaws and limitations** of JWTs — and how to mitigate them using secure practices like **asymmetric keys**, **token expiration**, and **rotation strategies**.


