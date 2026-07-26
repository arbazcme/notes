# 03 Authentication and Authorization

# Navbar

- [Authentication & Authorization](#authentication-and-authorization)
- [History of Authentication](#historical-context-of-authentication)
- [Passwords & Hashing](#password-hashing)
- [Public Key Cryptography (PKI, RSA, Diffie-Hellman)](#public-key-cryptography)
- [Kerberos](#kerberos)
- [Authentication Factors (MFA, OTP, Biometrics)](#evolution-of-authentication-factors)
- [Passwordless Authentication & WebAuthn](#passwordless-authentication)
- [Zero Trust & Modern Authentication](#zero-trust)
- [Sessions](#sessions)
- [Session Storage & Scaling](#session-storage)
- [Distributed Sessions](#distributed-sessions)
- [JWT (JSON Web Token)](#jwt)
- [Access & Refresh Tokens](#access-tokens)
- [JWT Advantages & Disadvantages](#jwt-advantages)
- [Sessions vs JWT](#session-vs-jwt)
- [Authentication Flow Summary](#authentication-flow-summary)
- [Final Thoughts](#final-thoughts)

## Authentication and Authorization

Finally, my favorite topic is **Authentication and Authorization**.

This is probably one of those areas that we encounter every day.

I'm pretty sure all of us have used authentication screens before.

We have logged into different platforms.

We have signed up on different platforms.

These are basically called **authentication flows**.

To summarize everything in two sentences:

## Authentication

Authentication is a mechanism to assign an identity to a subject.

In technical terms, it answers the question:

> **Who are you?**

Authentication basically means identifying **who you are** in a given context.

The context can be:

- A platform
- An operating system
- Your phone
- Or any other system

The process of finding the answer to **"Who are you?"** is called **Authentication**.

---

## Authorization

In the same way, Authorization answers another question:

> **What can you do?**

In a particular context:

- What capabilities do you have?
- What permissions do you have?

In technical terms, the process of finding the answer to **"What can you do?"** is called **Authorization**.

These two sentences summarize the entire topic.

In this video, we are going to dive into different aspects and different components of Authentication and Authorization.

We will build a high-level and technical understanding of:

- How it works
- Why it is important
- Different components involved
- How complete authentication and authorization flows are built
- Everything surrounding these concepts

---

# Historical Context of Authentication

Before diving into modern authentication, let's first understand its history.

How did authentication begin?

How did we reach the authentication methods that we use today?

---

## Authentication in Pre-Industrial Societies

The story begins in pre-industrial societies.

Authentication was mostly **intrinsic**, meaning it was implied rather than explicitly verified.

A person's identity was the same as that person's recognition within the community.

For example:

Suppose there is a respected village elder.

That elder could vouch for another person.

Deals and agreements were often sealed with a handshake.

The handshake symbolized:

- Mutual recognition
- Mutual trust
- Mutual agreement

Although this method was very simple, it was still a form of authentication.

It relied completely on **human contextual trust**.

Authentication during those times was based on people trusting one another.

That is how the earliest authentication methods worked.

---

## The Scaling Problem

As populations grew, interactions expanded beyond familiar communities.

People started interacting with strangers from different villages, regions, countries, and even continents.

The old trust-based authentication system could not scale.

Why?

Because a village elder who is trusted inside one village is not automatically trusted everywhere else.

Implicit trust mechanisms simply could not scale to larger societies.

This marked the beginning of the search for **explicit authentication**.

Earlier authentication was **implicit**, because it relied on trust.

Now people needed explicit proof of identity that worked independently of personal acquaintance.

Identity should no longer depend on someone else recognizing you.

This eventually led toward the era of seals and early cryptographic mechanisms.

---

# Wax Seals — Early Authentication Tokens

During the medieval period, society required an authentication system that could scale beyond personal recognition.

People invented **wax seals**.

If you've watched historical movies or TV series, you've probably seen wax seals attached to letters or agreements.

These seals acted as a form of authentication.

They were also an early cryptographic mechanism.

Each seal had a unique pattern.

They were attached to documents and functioned much like signatures.

Today we use:

- Handwritten signatures
- Digital signatures

During medieval times, wax seals played the same role.

They became one of the first widely adopted authentication tokens.

These seals were physical representations of identity.

Authentication relied on **something you possessed**.

If you possessed the seal, you were considered authenticated.

The seal could be used to verify your identity.

---

## Weaknesses of Wax Seals

Wax seals also had vulnerabilities.

They could be forged.

Forgery became one of the earliest recorded authentication bypass attacks.

A bypass attack simply means skipping authentication through malicious means.

Forging someone else's seal was an authentication bypass attack during medieval times.

Because of these weaknesses, people developed more sophisticated authentication mechanisms.

Examples included:

- Watermarks
- Encrypted codes
- Secure trade documentation

These ideas laid the foundation for cryptographic thinking.

People started thinking about:

- Codes
- Preventing forgery
- Preventing fraud
- Building stronger authentication systems

---

# Passphrases and Shared Secrets

During the Industrial Revolution, communication systems evolved rapidly.

One major invention was the **Telegraph**.

The telegraph became critical infrastructure.

Because messages were now traveling over communication systems, secure message validation became necessary.

Telegraph operators used **pre-agreed passphrases**.

You can think of these passphrases as early passwords.

These passphrases represented **shared secrets**.

They worked similarly to static passwords.

The password never changed.

Everyone agreed on one fixed secret and used it repeatedly.

Earlier authentication relied on:

> Something you possessed.

For example:

- A wax seal
- A physical token

Now authentication evolved into:

> Something you know.

Instead of showing a physical object, you proved your identity using knowledge stored in your brain.

That knowledge could be communicated:

- Verbally
- In writing

This became the foundation of modern passwords.

---

# The Beginning of Digital Authentication

Skipping ahead to modern computing...

Authentication entered its first digital phase with **mainframe computers** during the mid-20th century.

A famous milestone occurred in **1961**.

Researchers at MIT's Project MAC were working on the **Compatible Time-Sharing System (CTSS).**

Their goal was to allow multiple users to share the same computer without sharing each other's data.

To accomplish this, they introduced passwords for multi-user systems.

However, passwords were stored in **plain text**.

This turned out to be a serious security vulnerability.

One day, someone simply printed the password file.

Since it was stored as plain text, every password became visible.

This incident became one of the most important moments in authentication history.

It established the idea that:

> Passwords should never be stored in plain text.

This incident motivated secure password storage techniques.

---

# Password Hashing

Modern systems store passwords using **hashing**.

Hashing uses a cryptographic algorithm.

The algorithm performs computations on the original password.

Input:

```text
Plain Text Password
```

Output:

```text
Hash Value
```

A hashing algorithm has several important properties:

- It always produces the same output for the same input.
- The output has a fixed length.
- Whether the password contains 3 characters or 100 characters, the hash length remains the same.
- It is computationally infeasible to reverse the hash back into the original password.

These cryptographic hashing algorithms became the foundation of secure password storage.

Authentication also began aligning with the core principles of information security:

- Confidentiality
- Integrity
- Availability (CIA)

---

# Information Security Principles

Authentication became one of the core pillars of Information Security.

The security of authentication mechanisms depends heavily on cryptographic primitives.

Some of the major principles are:

- Confidentiality
- Integrity
- Availability

Authentication systems are designed while keeping these principles in mind.

---

# Public Key Cryptography

The next major breakthrough in authentication came with **Public Key Cryptography**.

Earlier authentication relied on:

- Something you have
- Something you know

Now authentication started using mathematics.

Public Key Cryptography introduced two different keys:

- Public Key
- Private Key

These two keys are mathematically related.

Anything encrypted with one key can only be decrypted using the other corresponding key.

Unlike passwords, the private key is never shared.

Only the public key is distributed.

This became the foundation of many modern authentication systems.

---

## Diffie-Hellman Key Exchange

One of the biggest problems before Public Key Cryptography was:

> How do two strangers agree upon a secret key without already sharing one?

Diffie-Hellman solved this problem.

Instead of directly exchanging a secret, both parties exchange mathematical values.

Using those exchanged values, both independently compute the exact same secret key.

The secret itself is never transmitted over the network.

Even if someone observes every exchanged message, deriving the shared secret remains computationally infeasible.

This was one of the biggest breakthroughs in secure communication.

---

# RSA

Another major breakthrough was RSA.

RSA uses a pair of keys:

- Public Key
- Private Key

If data is encrypted using the public key, only the corresponding private key can decrypt it.

Likewise, if something is digitally signed using the private key, anyone possessing the public key can verify that signature.

This allows authentication without revealing private secrets.

RSA became one of the foundations of modern secure communication.

---

# Public Key Infrastructure (PKI)

Public Key Infrastructure was introduced to solve another problem.

Suppose someone sends you their public key.

How do you know that the key actually belongs to them?

Someone else could pretend to be that person and provide a fake public key.

To solve this problem, trusted organizations called **Certificate Authorities (CAs)** were introduced.

Certificate Authorities verify identities.

After verification, they issue **Digital Certificates**.

A Digital Certificate binds:

- Identity
- Public Key

Because everyone trusts the Certificate Authority, everyone can also trust the certificate issued by it.

This entire ecosystem is known as Public Key Infrastructure (PKI).

---

# Digital Certificates

A Digital Certificate usually contains:

- Owner information
- Public Key
- Certificate Authority information
- Expiration time
- Digital Signature of the Certificate Authority

Whenever your browser connects to an HTTPS website, the server sends its Digital Certificate.

The browser verifies:

- Whether the certificate is valid.
- Whether it has expired.
- Whether it was issued by a trusted Certificate Authority.

If all checks pass, the browser trusts the server.

---

# Kerberos

As organizations grew larger, another authentication challenge appeared.

Imagine a company with hundreds of servers.

Logging into every server separately becomes inconvenient.

Kerberos was introduced to solve this problem.

Kerberos uses a trusted third party called the **Key Distribution Center (KDC).**

Instead of authenticating with every service individually, the user authenticates once with the KDC.

The KDC then issues tickets.

Those tickets are presented to different services.

The services trust the tickets issued by the KDC.

Therefore, users do not repeatedly enter passwords.

---

## Kerberos Tickets

The authentication flow looks like this:

User

↓

Authenticate with KDC

↓

Receive Ticket

↓

Present Ticket to Service

↓

Service verifies Ticket

↓

Access Granted

The password is not sent repeatedly to every service.

Only trusted tickets are exchanged.

This greatly improves both usability and security.

---

# Evolution of Authentication Factors

Authentication methods gradually evolved into multiple categories.

## Something You Know

Examples:

- Password
- PIN
- Passphrase

---

## Something You Have

Examples:

- Phone
- Smart Card
- Hardware Token
- Security Key

---

## Something You Are

Examples:

- Fingerprint
- Face Recognition
- Iris Scan
- Retina Scan
- Voice Recognition

These are called biometric authentication methods.

---

# Multi-Factor Authentication (MFA)

Using only one authentication factor is often insufficient.

Suppose someone steals your password.

If password authentication is the only protection, the attacker immediately gains access.

Multi-Factor Authentication combines multiple independent factors.

For example:

Password

+

OTP on Mobile Phone

Or

Password

+

Fingerprint

Or

Password

+

Security Key

Authentication succeeds only when all required factors are verified.

This dramatically increases security.

---

# One-Time Passwords (OTP)

A One-Time Password is valid only once.

It usually expires within a short duration.

Examples include:

- SMS OTP
- Email OTP
- Authenticator Applications

Even if someone learns an OTP later, it becomes useless after expiration.

---

# Time-based One-Time Passwords (TOTP)

Many authenticator applications generate Time-based One-Time Passwords.

These passwords change every few seconds.

Both the server and the authenticator application independently generate the same value using:

- Shared Secret
- Current Time

Since both sides perform the same computation, they obtain identical codes.

The password continuously changes, making replay attacks significantly harder.

---

# Biometrics

Biometric authentication relies on physical or behavioral characteristics.

Examples include:

- Fingerprint
- Face
- Iris
- Voice

Unlike passwords, biometric information cannot easily be forgotten.

Authentication becomes faster and more convenient.

However, biometrics introduce their own challenges.

---

## Challenges of Biometrics

Biometric data is permanent.

If someone steals your password:

You simply change the password.

If someone steals your fingerprint template:

You cannot replace your fingerprint.

Therefore biometric information must be stored with extremely high security.

Privacy also becomes a major concern.

---

# Passwordless Authentication

The future of authentication is moving toward passwordless systems.

Instead of remembering passwords, users authenticate using:

- Security Keys
- Trusted Devices
- Biometrics

This improves:

- User Experience
- Security

At the same time.

---

# WebAuthn

WebAuthn is a modern authentication standard.

It enables browsers and devices to authenticate users using cryptographic credentials instead of passwords.

Private keys remain securely stored on the user's device.

Only cryptographic proofs are exchanged with servers.

This significantly reduces phishing attacks.

---

# Zero Trust

Traditional security assumed:

"If you are inside the network, you can be trusted."

Zero Trust rejects this assumption.

Instead, every request is verified.

Regardless of where it originates.

Authentication and Authorization happen continuously.

Trust is never assumed.

It is always verified.

---

# Decentralized Identity

Another emerging idea is Decentralized Identity.

Instead of organizations completely controlling identities, users own their credentials.

Users selectively share only the information required.

This reduces dependence on centralized identity providers.

---

# Post-Quantum Cryptography

Current public-key cryptographic algorithms may become vulnerable to sufficiently powerful quantum computers.

Researchers are developing Post-Quantum Cryptography.

These algorithms aim to remain secure even against quantum attacks.

Authentication systems of the future are expected to gradually adopt these algorithms.

---

# Modern Authentication

Today, authentication is no longer just about passwords.

Modern systems combine:

- Cryptography
- Certificates
- Public Keys
- Biometrics
- Security Keys
- Multi-Factor Authentication
- Trusted Hardware
- Continuous Verification

Authentication has evolved from simple trust between people into mathematically verified digital identities that operate securely across the entire Internet.

---

# Sessions

Now that we understand authentication itself, the next topic is **Sessions**.

Authentication answers:

> Who are you?

A Session answers another question:

> How does the server remember that you are already authenticated?

To understand sessions, we first need to understand HTTP.

---

# Why HTTP is Stateless

Before understanding Sessions, we first need to understand an important property of HTTP.

HTTP is a **stateless protocol**.

Stateless means:

Every request is completely independent.

The server does **not** automatically remember previous requests.

For example:

```
Client
   │
   ├── Request 1 ─────────► Server
   │                        Response
   │
   ├── Request 2 ─────────► Server
   │                        Response
   │
   └── Request 3 ─────────► Server
                            Response
```

From the server's perspective:

- Request 1 is independent.
- Request 2 is independent.
- Request 3 is independent.

The server treats each request as if it has never seen the client before.

---

## Why was HTTP designed this way?

Keeping servers stateless makes them:

- Simple
- Fast
- Scalable

The server doesn't need to store information about millions of connected users.

Each request contains everything necessary to process it.

This design works well for simple document retrieval.

However, it introduces a major problem for applications.

---

# The Authentication Problem

Imagine a user visits a website.

The user enters:

- Username
- Password

The server verifies both.

Authentication succeeds.

Now the user clicks another page.

Since HTTP is stateless, the second request contains no memory of the login.

From the server's perspective:

This could be an entirely different person.

If nothing else is done, the user would need to log in again for every single request.

Example:

```
Login

↓

Authenticated

↓

Open Dashboard

↓

Login Again

↓

Open Profile

↓

Login Again

↓

Open Settings

↓

Login Again
```

Obviously, this would be terrible.

A mechanism was needed so the server could remember authenticated users.

That mechanism is called a **Session**.

---

# What is a Session?

A Session is a way for the server to remember a client across multiple HTTP requests.

Authentication usually happens once.

After successful authentication, the server creates a Session.

The Session stores information such as:

- User ID
- Login Time
- Expiration Time
- Permissions
- Other user-related information

Instead of asking for the password every time, the server simply checks the Session.

---

# Session Creation

Suppose a user logs in.

```
Username

Password

↓

Server
```

The server verifies the credentials.

If they are correct:

The server creates a Session.

Example:

```
Session ID

ABCD123456XYZ
```

This Session ID uniquely identifies the logged-in user.

The actual Session data stays on the server.

Example:

```
Session ID

↓

User ID

↓

Role

↓

Expiration Time

↓

Permissions
```

The client never receives this internal Session data.

The client only receives the Session ID.

---

# Session ID

The Session ID is simply a unique identifier.

It does not normally contain:

- Username
- Password
- Role
- Sensitive Information

Instead, it acts like a reference.

Think of it as a ticket number.

Imagine checking into a hotel.

The receptionist stores your information.

You receive only a room key.

The room key does not contain all your personal information.

It simply points to your stored record.

A Session ID works similarly.

---

# Where is the Session ID Stored?

The Session ID is usually stored inside a **Cookie**.

The flow looks like this:

```
Client

↓

Login Request

↓

Server verifies credentials

↓

Server creates Session

↓

Server sends Session ID

↓

Browser stores Cookie

↓

Future requests automatically include Cookie
```

The browser automatically attaches the Cookie to future requests.

The user does not manually send it.

---

# Cookies

A Cookie is a small piece of data stored inside the browser.

Servers use Cookies for many purposes.

Examples include:

- Session IDs
- User Preferences
- Language
- Theme
- Analytics

Cookies themselves are not authentication.

They simply transport data between the browser and the server.

Most authentication systems use Cookies to carry Session IDs.

---

# Session Flow

The complete Session flow looks like this:

```
Client

↓

Login

↓

Server authenticates

↓

Create Session

↓

Generate Session ID

↓

Return Cookie

↓

Browser stores Cookie

↓

Every Request

↓

Cookie Sent Automatically

↓

Server finds Session

↓

User Recognized
```

Notice something important.

The Session data remains on the server.

Only the identifier travels between the browser and server.

---

# Looking Up Sessions

Suppose the browser sends:

```
Cookie

↓

Session ID
```

The server receives the Session ID.

It searches its Session Store.

Example:

```
Session Store

ABC123

↓

User 17

↓

Authenticated
```

If the Session exists:

The request continues.

Otherwise:

The user must authenticate again.

---

# Session Expiration

Sessions should not last forever.

Suppose someone leaves a computer unlocked.

If Sessions never expired:

Anyone could continue using the account indefinitely.

Therefore Sessions usually have expiration times.

Examples:

- 15 minutes
- 30 minutes
- 1 hour
- 24 hours

When the Session expires:

The Session is deleted.

The user must log in again.

---

# Logout

When a user logs out:

The Session should be destroyed.

Example:

```
Logout

↓

Delete Session

↓

Delete Cookie

↓

Authentication Ends
```

Even if someone still possesses the old Session ID, it no longer points to a valid Session.

---

# Session Storage

Where are Sessions actually stored?

There are multiple possibilities.

---

## File-based Sessions

The simplest approach is storing Sessions inside files.

Example:

```
Session ID

↓

session_abc.txt
```

Each Session becomes a separate file.

Advantages:

- Simple
- Easy to implement

Disadvantages:

- Slow
- Difficult to scale
- Poor performance with many users

---

## Database Sessions

Instead of files, Sessions can be stored inside a database.

Each Session becomes one database record.

Example:

```
Session Table

Session ID

User ID

Expiration

Created Time
```

Advantages:

- Persistent
- Easy to query

Disadvantages:

Database access is slower than memory.

---

## Memory-based Sessions

A much faster approach stores Sessions entirely in memory.

Since RAM is much faster than disks or databases, Session lookups become extremely fast.

This is why many production systems use in-memory storage.

One of the most popular choices is Redis.

---

## Redis Sessions

Redis is an in-memory key-value database.

Instead of searching files or tables:

The server directly asks Redis.

```
Session ID

↓

Redis

↓

Session Data
```

Since everything is stored in RAM:

Lookups are extremely fast.

Redis has therefore become one of the most common Session stores in modern backend systems.

---

# Sessions in Distributed Systems

Now imagine this architecture.

```
             Load Balancer
             /          \
            /            \
      Server A        Server B
```

Suppose Login happens on Server A.

The Session is stored only on Server A.

Now the next request goes to Server B.

Server B has never seen that Session.

From its perspective:

The user appears unauthenticated.

This becomes a serious problem in distributed systems.

---

# Distributed Sessions

In distributed systems, storing Sessions locally on each server creates a problem.

Consider this architecture:

```text
              Load Balancer
             /             \
            /               \
      Server A           Server B
```

Suppose the user logs in.

The request reaches **Server A**.

```
Login

↓

Server A

↓

Session Created
```

Now the Session exists only on **Server A**.

The next request is sent through the Load Balancer.

This time the request reaches **Server B**.

```
Browser

↓

Load Balancer

↓

Server B
```

Server B tries to find the Session.

But it never created it.

Therefore:

```
Session Not Found

↓

User Appears Logged Out
```

Obviously, this is incorrect.

The user already logged in.

This happens because Session state is stored only on one server.

---

# Centralized Session Storage

The solution is to move Session storage outside the application servers.

Instead of every server maintaining its own Sessions:

All servers share the same Session Store.

Example:

```text
                  Load Balancer
                 /             \
                /               \
          Server A          Server B
                \              /
                 \            /
                  Redis
```

Now the flow becomes:

```
Login

↓

Server A

↓

Store Session in Redis
```

Next request:

```
Browser

↓

Server B

↓

Read Session From Redis
```

Both servers can access exactly the same Session.

This completely solves the synchronization problem.

Redis became extremely popular because of this use case.

---

# Sticky Sessions

Another solution is called **Sticky Sessions**.

Instead of sharing Sessions:

The Load Balancer always sends one user to the same server.

Example:

```
User A

↓

Server A

Always
```

```
User B

↓

Server B

Always
```

This works because every request reaches the same machine.

However, Sticky Sessions introduce new problems.

If Server A crashes:

All Sessions stored there disappear.

Users must log in again.

Sticky Sessions also reduce load balancing flexibility.

Therefore, centralized Session storage is generally preferred.

---

# Problems With Sessions

Sessions work well.

But they also have disadvantages.

## Server Memory

The server must store every active Session.

For example:

```
1 Million Users

↓

1 Million Sessions
```

Memory consumption increases continuously.

---

## Horizontal Scaling

When application servers increase:

Session synchronization becomes necessary.

Without centralized storage:

Authentication breaks.

```text
           Load Balancer
          /     |      \
         ▼      ▼       ▼
    Server A Server B Server C
         \      |      /
          ▼     ▼     ▼
             Redis
Increasing servers "computation" to handle users

```

---

## Session Replication

Some systems replicate Sessions across servers.

Example:

```
Server A

↓

Copy Session

↓

Server B

↓

Copy Session

↓

Server C
```

Replication introduces:

- Network Overhead
- Synchronization Complexity
- Extra Memory Usage

---

## Stateless Services

Modern cloud applications prefer stateless services.

Stateless services are easier to:

- Scale
- Deploy
- Replace
- Recover

Traditional Sessions introduce server-side state.

This motivated another authentication approach.

That approach is called **JWT**.

---

# JWT

JWT stands for:

**JSON Web Token**

JWT is a way of carrying authentication information directly inside a token.

Instead of storing Session data on the server:

The token itself stores user information.

---

# Why JWT Was Introduced

Suppose we eliminate Session storage completely.

Instead of:

```
Session ID

↓

Server Memory
```

We send all necessary information to the client.

Example:

```
User

↓

Login

↓

JWT

↓

Browser
```

Now every future request simply sends the JWT.

The server does not need Session storage.

This is called **Stateless Authentication**.

---

# Stateless Authentication

Every request contains everything necessary for authentication.

Example:

```
Browser

↓

JWT

↓

Server

↓

Verify JWT

↓

Authenticated
```

Notice:

The server does not search:

- Redis
- Database
- Memory

Authentication becomes completely independent.

---

# JWT Structure

A JWT consists of three parts.

```
Header

.

Payload

.

Signature
```

These three components are joined using dots.

Example:

```
xxxxx.yyyyy.zzzzz
```

---

# Header

The Header contains metadata.

Example:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

It specifies:

- Token Type
- Signing Algorithm

---

# Payload

The Payload contains claims.

Claims are pieces of information.

Example:

```json
{
  "userId": 42,
  "username": "john",
  "role": "admin"
}
```

The Payload may also contain:

- Email
- Permissions
- Expiration Time
- Issued Time

However:

Sensitive information should never be stored directly inside the Payload.

---

# Signature

The Signature protects the token from modification.

The server signs the Header and Payload using a secret key.

Example:

```
Header

+

Payload

+

Secret Key

↓

Hash Function

↓

Signature
```

If anyone modifies even one character:

The Signature changes.

Verification immediately fails.

---

# Base64 Encoding

JWT components are Base64 encoded.

Example:

```
Header

↓

Base64
```

```
Payload

↓

Base64
```

These encoded values are concatenated together.

Important:

Base64 is **not encryption**.

It is only an encoding format.

Anyone can decode:

- Header
- Payload

Only the Signature cannot be forged without the secret key.

---

# JWT Login Flow

Complete authentication flow:

```
Login Request

↓

Server

↓

Verify Username

↓

Verify Password

↓

Generate JWT

↓

Return JWT

↓

Browser Stores JWT
```

Every future request:

```
Browser

↓

Authorization Header

↓

Bearer JWT

↓

Server
```

The server verifies the Signature.

If valid:

Authentication succeeds.

---

# Authorization Header

JWT is commonly sent inside:

```text
Authorization: Bearer <token>
```

The word **Bearer** simply means:

"The holder of this token is requesting access."

The server extracts the token.

Verifies it.

Then authenticates the request.

---

# JWT Verification

Whenever a request arrives:

```
Receive JWT

↓

Extract Header

↓

Extract Payload

↓

Recompute Signature

↓

Compare Signature

↓

Valid?

↓

Authenticated
```

If verification fails:

Reject the request.

---

# Expiration

JWTs usually contain an expiration claim.

Example:

```
exp
```

After expiration:

The server rejects the token.

The user must obtain a new one.

This limits damage if a token is stolen.

---

# Access Tokens

Access Tokens are short-lived JWTs.

Example lifetime:

- 10 minutes
- 15 minutes
- 30 minutes
- 1 hour

These tokens authorize API requests.

Because they expire quickly:

Security improves.

---

# Refresh Tokens

Refresh Tokens solve another problem.

Imagine Access Tokens expire every 15 minutes.

Without Refresh Tokens:

Users would log in every 15 minutes.

Instead:

```
Access Token

Expires

↓

Refresh Token

↓

Generate New Access Token
```

The Refresh Token remains valid much longer.

Examples:

- 7 days
- 30 days
- 90 days

Users stay logged in without repeatedly entering passwords.

---

# Refresh Token Flow

A typical authentication flow using Access Tokens and Refresh Tokens looks like this:

```text
User Login

↓

Verify Credentials

↓

Generate Access Token

+

Generate Refresh Token

↓

Return Both Tokens

↓

Client Stores Tokens
```

Whenever the client makes an API request:

```text
Client

↓

Access Token

↓

Server

↓

Verify Token

↓

Response
```

If the Access Token expires:

```text
Access Token Expired

↓

Client Sends Refresh Token

↓

Server Verifies Refresh Token

↓

Generate New Access Token

↓

Return New Access Token
```

The user does not need to log in again.

---

# Why Two Tokens?

Why don't we simply create one JWT with a very long expiration?

For example:

```
Expiration = 1 Year
```

If someone steals that token:

They immediately gain access to the account for an entire year.

This is a huge security risk.

Instead:

- Access Tokens are short-lived.
- Refresh Tokens are long-lived.

If an Access Token is compromised:

Its lifetime is very small.

This greatly limits potential damage.

---

# JWT Advantages

JWT provides several advantages.

## Stateless

The server does not maintain Session state.

Every request carries authentication information.

Servers remain stateless.

---

## Easy Horizontal Scaling

Since there are no Sessions:

Adding more servers becomes very simple.

Example:

```text
          Load Balancer

        /      |      \

     API1    API2    API3
```

Every server independently verifies JWTs.

No Session synchronization is required.

---

## Reduced Database Lookups

Traditional Sessions require:

```text
Request

↓

Session ID

↓

Redis

↓

Session

↓

Continue
```

JWT verification usually requires:

```text
Request

↓

Verify Signature

↓

Continue
```

No Session lookup.

No Redis lookup.

No Session database.

---

## Microservices

JWT works extremely well in Microservice architectures.

Example:

```text
Client

↓

API Gateway

↓

User Service

↓

Order Service

↓

Payment Service
```

Each service independently verifies the same JWT.

Authentication becomes decentralized.

---

# JWT Disadvantages

JWT also has disadvantages.

---

## Token Revocation

Suppose a JWT expires after one hour.

Now imagine the user clicks:

```
Logout
```

The token still exists.

Until expiration:

The token remains valid.

The server has no Session to delete.

This becomes one of the biggest disadvantages of JWT.

---

## Large Payload

Since JWT carries user information:

Large payloads produce large tokens.

Every request sends the entire token.

This increases network usage.

Therefore Payloads should remain small.

---

## Cannot Change Claims Easily

Suppose a JWT contains:

```json
{
    "role":"admin"
}
```

Now the user's role changes.

The existing JWT still contains:

```
admin
```

The server cannot modify an already-issued JWT.

A new token must be generated.

---

## Secret Key Security

JWT verification depends on a secret key.

If the secret key is compromised:

Attackers can generate valid JWTs.

Therefore the signing key must be stored securely.

---

# JWT Blacklisting

Sometimes applications maintain a blacklist.

Example:

```text
Logout

↓

Store JWT ID

↓

Blacklist
```

Whenever a request arrives:

```
Verify JWT

↓

Check Blacklist

↓

Allowed?
```

If the token exists in the blacklist:

Reject it.

This provides immediate logout.

However:

Maintaining a blacklist introduces server-side state again.

Partially defeating JWT's stateless advantage.

---

# Session vs JWT

## Sessions

```text
Browser

↓

Session ID

↓

Server

↓

Session Store

↓

User Information
```

### Characteristics

- Server stores state.
- Client stores only Session ID.
- Easy Logout.
- Easy Revocation.
- Requires Session storage.
- Requires Session synchronization.

---

## JWT

```text
Browser

↓

JWT

↓

Server

↓

Verify Signature
```

### Characteristics

- Stateless.
- No Session storage.
- Easy horizontal scaling.
- Good for APIs.
- Difficult immediate logout.
- Difficult revocation.

---

# Sessions vs JWT Comparison

| Sessions | JWT |
|----------|-----|
| Stateful | Stateless |
| Server stores Session | Client stores Token |
| Session lookup required | Signature verification only |
| Easy logout | Logout is harder |
| Easy revocation | Revocation requires blacklist |
| Needs Redis/Database | No Session Store |
| Better for traditional web apps | Better for APIs and Microservices |

---

# Which One Should You Use?

There is no universally better option.

Choose based on your application's requirements.

Use **Sessions** when:

- Building traditional server-rendered applications.
- Immediate logout is important.
- Session revocation is required.
- Centralized authentication is acceptable.

Use **JWT** when:

- Building REST APIs.
- Building Microservices.
- Horizontal scaling is important.
- Stateless architecture is preferred.
- Mobile applications require authentication.
- Multiple backend services consume the same authentication token.

Many modern applications combine both approaches.

For example:

- Session authentication for web applications.
- JWT authentication for APIs.

---

# Authentication Flow Summary

The complete authentication lifecycle can be summarized as follows:

```text
User

↓

Signup

↓

Login

↓

Credential Verification

↓

Authentication Successful

↓

Generate Session

OR

Generate JWT

↓

Client Stores Session Cookie

OR

Client Stores JWT

↓

Future Requests

↓

Authentication Verification

↓

Authorization

↓

Protected Resource
```

---

# Authentication vs Authorization

Authentication answers:

> **Who are you?**

Authorization answers:

> **What are you allowed to do?**

Example:

```text
User Logs In

↓

Authentication

↓

Identity Verified

↓

Authorization

↓

Check Permissions

↓

Access Granted
```

Another example:

```text
User

↓

Authenticated

↓

Role = Admin

↓

Delete User

↓

Allowed
```

```text
User

↓

Authenticated

↓

Role = Student

↓

Delete User

↓

Denied
```

Notice:

Both users are authenticated.

Only one is authorized.

Authentication always comes before Authorization.

---

# Complete Picture

Putting everything together:

```text
User

↓

Login

↓

Authentication

↓

Identity Verified

↓

Session / JWT Created

↓

Future Requests

↓

Authentication Verified

↓

Authorization Check

↓

Protected API

↓

Response
```

---

# Final Thoughts

Authentication has evolved significantly over time.

The evolution can be summarized as:

```text
Human Trust

↓

Wax Seals

↓

Shared Secrets

↓

Passwords

↓

Password Hashing

↓

Public Key Cryptography

↓

Certificates

↓

Kerberos

↓

Sessions

↓

Cookies

↓

JWT

↓

Multi-Factor Authentication

↓

Passwordless Authentication

↓

WebAuthn

↓

Zero Trust
```

Modern authentication is no longer just about passwords.

It combines:

- Cryptography
- Secure Password Storage
- Public Key Infrastructure
- Certificates
- Multi-Factor Authentication
- Sessions
- JWT
- Biometrics
- Hardware Security Keys
- Continuous Authorization

Authentication identifies **who you are**.

Authorization determines **what you are allowed to do**.

Together, they form the foundation of security in modern web applications.


# Authentication & Authorization Revision

---

# 1. OAuth (Authorization)

## What problem does OAuth solve?

Without OAuth:

User → Gives Google password → Your App ❌

Bad because your app should never know the user's Google password.

OAuth solves this.

User logs into Google directly.

Google asks:

"Allow this app to access your data?"

If the user accepts,

Google returns an **Access Token**.

Your app uses the Access Token to access the allowed Google resources.

Example:

Your App
↓
Redirect to Google
↓
User logs into Google
↓
User grants permission
↓
Google returns Access Token
↓
Your App accesses user's email/profile

### Remember

- OAuth = Authorization
- Grants permission to access resources
- Does NOT tell your app who the user is

---

# 2. OIDC (OpenID Connect)

Problem:

OAuth gives permission,

but it doesn't tell your app who the user is.

OIDC solves this.

OIDC = OAuth + Identity.

Google returns:

- Access Token
- ID Token (JWT)

The ID Token contains:

- User ID
- Name
- Email

Now your app knows who logged in.

### Remember

OAuth → What can this app access?

OIDC → Who is this user?

Login with Google = OIDC

---

# 3. Which Authentication Should I Choose?

## Traditional Website

Examples:

- Banking
- ERP
- Admin Dashboard

Use:

- Sessions
- Cookies

Reason:

Easy logout, browser friendly.

---

## REST APIs

Examples:

- Mobile Apps
- React + Backend

Use:

- JWT
- Refresh Token (Hybrid)

Reason:

Fast, scalable, stateless.

---

## Microservices

Use:

- JWT

Reason:

Each service verifies JWT locally.

---

## Login with Google

Use:

- OIDC

---

## Public APIs

Examples:

- OpenAI
- Stripe
- Google Maps

Use:

- API Keys

Reason:

Authenticate applications, not users.

---

# 4. Authorization

Authentication answers:

"Who are you?"

Authorization answers:

"What are you allowed to do?"

Example:

User logs in
↓
Authenticated
↓
Check role
↓
Allow or Deny

Example:

Student
✓ View Courses
✗ Delete Users

Admin
✓ Everything

---

## RBAC (Role-Based Access Control)

Roles:

- Admin
- Teacher
- Student

Permissions depend on role.

Most applications use RBAC.

---

# 5. Error Messages

Never reveal what was wrong.

❌ Username doesn't exist.

❌ Wrong password.

These help attackers.

Always return:

✅ Invalid username or password.

---

# 6. Timing Attacks

Bad implementation:

Wrong username → 5 ms

Wrong password → 40 ms

Attacker measures response time and learns whether a username exists.

Solution:

Always perform roughly the same amount of work before responding.

Return generic errors.

Both requests should take nearly the same time.

---

# Quick Revision

## Authentication

Who are you?

---

## Authorization

What are you allowed to do?

---

## Sessions

- Stateful
- Server stores session
- Easy logout

Best for traditional websites.

---

## JWT

- Stateless
- Server stores nothing
- Fast
- Cannot revoke instantly

Best for APIs.

---

## Hybrid (Recommended)

Access Token (JWT)

- Short-lived
- Sent on every request

Refresh Token

- Long-lived
- Stored in Redis/DB
- Creates new JWT
- Can be revoked

Best of both worlds.

---

## API Key

Used by applications/services.

Not for user login.

---

## OAuth

Permission to access another application's resources.

---

## OIDC

OAuth + User Identity.

Used for "Login with Google."

---

## Golden Interview Line

Authentication = Verify Identity.

Authorization = Verify Permissions.

OAuth = Permission.

OIDC = Login.

Session = Stateful.

JWT = Stateless.

Hybrid = JWT + Refresh Token.

API Key = Application Authentication.
