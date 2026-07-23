# LEC 15 - Backend Security (Part 1) - Security Mindset & Injection Attacks

# Why Security Matters

A backend application handles

- User accounts
- Payments
- Personal information
- Business data
- Internal services

A single vulnerability can lead to

- Data leaks
- Financial loss
- Account takeover
- Service disruption
- Reputation damage

Security is not a feature.

It is a design principle.

---

# Security Mindset

The biggest mistake developers make is assuming users behave correctly.

Attackers do the opposite.

They

- Modify requests
- Change parameters
- Send unexpected inputs
- Try invalid data
- Explore every possible edge case

Instead of asking

> "Will this work?"

always ask

> "How can someone misuse this?"

---

# Think Like an Attacker

Attackers are not interested in your programming language.

They only care about one question:

> **What assumptions did the developer make?**

Common assumptions

- User input is valid.
- Requests come only from the frontend.
- Users won't modify API requests.
- Inputs contain only expected values.
- Nobody will inspect network requests.

These assumptions create vulnerabilities.

---

# Never Trust User Input

Everything coming from outside your backend is **untrusted**.

Examples

- Form fields
- Query parameters
- Request body
- Cookies
- Headers
- Uploaded files
- URLs

Treat all of them as potentially malicious.

---

# The Root Cause of Most Injection Attacks

Your backend communicates with multiple systems.

```text
Browser

↓

Backend

↓

Database

↓

Operating System

↓

External Services
```

Each system understands a different language.

Examples

- SQL
- Shell Commands
- HTML
- XML
- JSON
- LDAP

Problems occur when **user input accidentally becomes executable code**.

---

# Code vs Data

This is the most important concept in backend security.

Correct

```text
User Input

↓

Treat as Data

↓

Safe
```

Wrong

```text
User Input

↓

Interpreted as Code

↓

Attack
```

Almost every injection attack comes from confusing **data** with **code**.

---

# What is an Injection Attack?

An Injection Attack occurs when user input changes the meaning of a command.

Instead of being treated as plain data,

it becomes executable.

Examples

- SQL Injection
- NoSQL Injection
- Command Injection
- LDAP Injection
- XML Injection

---

# SQL Injection

## What is SQL Injection?

SQL Injection happens when user input becomes part of an SQL query.

Unsafe example

```text
SQL Query

+

User Input

↓

One Combined String
```

If the input contains SQL syntax,

the database may execute it.

---

# Why It Happens

Developers build SQL queries using string concatenation.

Example

```text
SELECT ...

+

User Email

↓

One SQL String
```

If the email contains SQL commands,

the database cannot distinguish

- Actual SQL
- User data

---

# Possible Consequences

Attackers may

- Read all users
- Bypass authentication
- Delete tables
- Modify data
- Leak sensitive information

---

# The Root Cause

The database cannot determine

```text
What is SQL?

What is User Data?
```

Both become part of the same string.

---

# The Solution - Parameterized Queries ⭐

Instead of mixing SQL and user input,

keep them separate.

```text
SQL Query

↓

Placeholder

↓

User Data Sent Separately

↓

Database Combines Safely
```

The database knows

- SQL structure
- User values

independently.

User input is always treated as **data**.

---

# Why Parameterized Queries Work

Instead of

```text
Build SQL String Yourself
```

the database driver receives

```text
Query Template

+

Parameters
```

The driver safely inserts parameters

without allowing them to become SQL commands.

---

# Validation is Still Important

Parameterized queries prevent SQL Injection,

but validation should still verify

- Email format
- Number ranges
- String length
- Allowed values

Validation catches invalid data early.

Parameterized queries protect the database.

Both are required.

---

# Database Permissions

Even if an attacker succeeds,

the database account used by your application should have **minimum required permissions**.

Example

Allow

- SELECT
- INSERT
- UPDATE
- DELETE

Avoid giving permission for

- DROP TABLE
- CREATE DATABASE
- ALTER DATABASE

This follows the **Principle of Least Privilege**.

---

# NoSQL Injection

NoSQL databases can also suffer from injection attacks.

Example databases

- MongoDB
- CouchDB

The attack is different,

but the root cause is the same.

```text
User Data

↓

Becomes Query Logic

↓

Injection
```

Never pass user-controlled objects directly into database queries.

---

# Command Injection

## What is Command Injection?

Sometimes a backend executes operating system commands.

Examples

- Image processing
- Video conversion
- File compression
- Backup scripts

Unsafe design

```text
OS Command

+

User Input

↓

One Shell Command
```

If the user input contains shell operators,

extra commands may execute.

---

# The Solution

Never build shell commands using string concatenation.

Instead,

use APIs that separate

- Command
- Arguments

Example

```text
Command

↓

Arguments

↓

Operating System
```

Arguments are treated only as data.

---

# Injection Attack Mental Model

Whenever your backend talks to another system,

ask

```text
Can this user input become executable?
```

If yes,

find a safer API.

---

# Common Injection Targets

- SQL
- NoSQL
- Shell Commands
- LDAP
- XML
- HTML Templates

The same principle applies everywhere.

---

# Safe Design Principles

✅ Treat all user input as untrusted.

✅ Never concatenate user input into executable commands.

✅ Use parameterized queries.

✅ Use safe command execution APIs.

✅ Validate inputs.

✅ Apply least privilege to database accounts.

---

# Best Practices

- Never trust client input.
- Separate code from data.
- Validate every external input.
- Parameterize all database queries.
- Avoid raw SQL when possible.
- Never build shell commands manually.
- Grant minimum database permissions.
- Think like an attacker when designing APIs.

---

# Interview Takeaways

Know

- Why injection attacks happen
- Code vs Data concept
- SQL Injection
- NoSQL Injection
- Command Injection
- Parameterized Queries
- Least Privilege
- Safe command execution
- Why validation alone is insufficient

---

# Mental Model

```text
User Input

↓

Validation

↓

Parameterized API

↓

Database / OS

↓

Safe Execution
```

**Golden Rule**

> **Never let user input become executable code. Always separate commands from data.**

# LEC 15B - Backend Security (Part 2) - Authentication Security

# Authentication Security

Authentication answers one question

> **Who is this user?**

If authentication fails,

an attacker may

- Access another user's account
- Read private data
- Perform actions as someone else
- Steal money
- Take over accounts

Authentication is one of the most security-critical parts of any backend.

---

# Build vs Buy Authentication

For production applications,

using a trusted authentication provider is often a better choice than building everything yourself.

Authentication providers handle

- Session management
- OAuth (Google, GitHub, etc.)
- Password security
- Session revocation
- Multi-device login
- Security updates

Examples include services like Clerk, Auth0, Firebase Authentication, etc.

For learning and small projects,

building authentication yourself is valuable.

For production,

avoid reinventing it unless necessary.

---

# Password Storage

## Never Store Plain Text Passwords ❌

Wrong

```text
User Password

↓

Database
```

If the database leaks,

every password is exposed.

---

# Store Password Hashes ✅

Instead,

store a **hash**.

```text
Password

↓

Hash Function

↓

Store Hash
```

During login

```text
Entered Password

↓

Hash Again

↓

Compare Hashes
```

The original password is never stored.

---

# Hashing

A hash function converts input into a fixed-size value.

Properties

- One-way
- Deterministic
- Fast comparison
- Original password cannot be recovered

---

# Salting

Many users choose identical passwords.

Without salt

```text
password123

↓

Same Hash
```

Attackers can use precomputed lookup tables.

A **Salt** is random data added before hashing.

```text
Password

+

Random Salt

↓

Hash
```

Now,

even identical passwords generate different hashes.

---

# Use Strong Password Hashing Algorithms

Do **not** use

- MD5
- SHA1
- SHA256 (alone)

These are designed to be fast,

which helps attackers.

Use algorithms specifically built for password hashing

- bcrypt
- Argon2
- scrypt

These are intentionally slow,

making brute-force attacks expensive.

---

# Password Verification

Login flow

```text
User Password

↓

Hash With Stored Salt

↓

Compare With Stored Hash

↓

Authenticated
```

Never compare plain-text passwords.

---

# Password Policies

Require

- Minimum length
- Reasonable complexity
- Common password checks

Avoid unnecessary complexity rules that hurt usability.

Long passwords are generally more secure than overly complicated short ones.

---

# Stateful vs Stateless Authentication

## Stateless

Example

```text
JWT

↓

Client Stores Token

↓

Server Verifies Signature
```

Server stores no session.

Simple and scalable.

---

## Stateful

Server stores active sessions.

```text
Login

↓

Session Created

↓

Stored in Database / Redis

↓

Client Sends Session ID
```

Advantages

- Logout everywhere
- Session revocation
- Better session control

More infrastructure is required.

---

# Session Revocation

Users should be able to

- Logout
- Logout from all devices
- Revoke stolen sessions

Stateful authentication supports this naturally.

---

# Multi-Device Login

A user may be logged in from

- Laptop
- Mobile
- Tablet

Each session should be managed independently.

If one device is compromised,

only that session should need revocation.

---

# OAuth (Social Login)

Examples

- Google
- GitHub
- Microsoft

Instead of storing another password,

users authenticate through a trusted provider.

Benefits

- Better user experience
- Reduced password management
- Improved security

---

# Account Linking

A user may

- Register with email/password
- Later log in with Google

If both accounts belong to the same email,

they should be linked correctly.

Otherwise,

duplicate accounts may be created.

---

# Session Security

Sessions should

- Expire automatically
- Be revocable
- Be stored securely
- Use secure cookies when applicable

Long-lived sessions increase risk.

---

# Authentication vs Authorization

Authentication

```text
Who are you?
```

Authorization

```text
What are you allowed to do?
```

Never confuse the two.

A user may be authenticated,

but still not authorized to access certain resources.

---

# Common Authentication Mistakes

❌ Storing plain passwords

❌ Using weak hashing algorithms

❌ Forgetting password salts

❌ Long-lived sessions

❌ No logout mechanism

❌ Trusting client-side authentication

❌ Weak password policies

❌ Incorrect OAuth account linking

---

# Security Principles

Always assume

- Tokens may leak
- Sessions may be stolen
- Passwords may be guessed
- Devices may be compromised

Design authentication to minimize damage.

---

# Best Practices

- Never store plain passwords.
- Store only password hashes.
- Use bcrypt, Argon2, or scrypt.
- Use unique salts.
- Support secure session expiration.
- Allow session revocation.
- Use trusted OAuth providers.
- Correctly link social accounts.
- Separate authentication from authorization.

---

# Interview Takeaways

Know

- Password hashing
- Salting
- Why MD5/SHA are unsuitable for passwords
- bcrypt vs Argon2
- Stateful vs Stateless authentication
- Session revocation
- OAuth basics
- Multi-device sessions
- Authentication vs Authorization

---

# Mental Model

```text
User Login

↓

Validate Credentials

↓

Hash Password

↓

Compare Hash

↓

Create Session / Token

↓

Future Requests

↓

Authenticate

↓

Authorize
```

**Golden Rule**

> **Never store passwords. Store hashes. Never trust authentication alone—always perform authorization checks as well.**



# LEC 15C - Backend Security (Part 3) - Browser & API Security

# Browser Security

Even if your backend is secure,

the browser can still become an attack surface.

Common browser-related attacks

- XSS
- CSRF
- Cookie Theft
- Clickjacking
- Unsafe File Uploads

---

# Cross-Site Scripting (XSS)

## What is XSS?

XSS occurs when user input is treated as **HTML or JavaScript** instead of plain text.

Instead of displaying data,

the browser executes it.

---

# Why Does XSS Happen?

Root cause

```text
User Input

↓

Rendered as HTML

↓

Browser Executes It
```

Instead of

```text
User Input

↓

Displayed as Text
```

---

# Possible Consequences

An attacker may

- Steal cookies
- Hijack sessions
- Read sensitive page data
- Perform actions as the user
- Redirect users to malicious websites

---

# Types of XSS

## Stored XSS

Malicious content is stored in the database.

Example

```text
Attacker Posts Comment

↓

Stored

↓

Every Visitor Executes It
```

---

## Reflected XSS

Malicious script is immediately reflected back in the response.

Usually through

- Search boxes
- Query parameters
- Error pages

---

## DOM-based XSS

JavaScript running in the browser inserts unsafe content into the page.

No server response modification is required.

---

# Preventing XSS

- Escape HTML output.
- Sanitize user-generated HTML.
- Never trust user input.
- Avoid inserting raw HTML.
- Use safe templating libraries.
- Enable Content Security Policy (CSP).

---

# Content Security Policy (CSP)

CSP is an HTTP response header.

It tells browsers

which scripts,

styles,

and resources

are allowed to execute.

Even if an attacker injects JavaScript,

CSP can block execution.

---

# Cookies

Cookies store authentication information.

Sensitive cookies should be protected.

Recommended flags

- HttpOnly
- Secure
- SameSite

---

# HttpOnly

```text
JavaScript

↓

Cannot Read Cookie
```

Helps reduce cookie theft through XSS.

---

# Secure Cookie

Cookie is sent

only over HTTPS.

Never over plain HTTP.

---

# SameSite Cookie

Controls when browsers send cookies.

Options

## Strict

Only same-site requests.

Highest protection.

---

## Lax

Safe default.

Blocks most CSRF attacks while allowing normal navigation.

---

## None

Allows cross-site requests.

Must also use

```text
Secure
```

---

# Cross-Site Request Forgery (CSRF)

## What is CSRF?

The victim is already logged in.

The attacker tricks the victim's browser into sending requests.

Because cookies are automatically included,

the server believes the request came from the legitimate user.

---

# CSRF Flow

```text
Victim Logged In

↓

Visits Malicious Website

↓

Browser Sends Authenticated Request

↓

Backend Accepts Request

↓

Unauthorized Action
```

---

# Preventing CSRF

Use one or more of

- SameSite cookies
- CSRF Tokens
- Origin validation
- Referer validation

---

# CSRF Tokens

Workflow

```text
Server Generates Token

↓

Browser Stores Token

↓

Browser Sends Token

↓

Server Verifies Token
```

Without the token,

the request is rejected.

---

# Cross-Origin Resource Sharing (CORS)

Browsers normally block requests across different origins.

CORS allows servers to specify

which origins are permitted.

---

# CORS is NOT Security

Important

CORS only protects browsers.

It does **not**

protect your backend.

Attackers using

- curl
- Postman
- Scripts

completely bypass browser CORS restrictions.

Authentication and authorization are still required.

---

# Safe CORS Configuration

Allow

only trusted origins.

Avoid

```text
*
```

when credentials or cookies are involved.

---

# File Upload Security

File uploads are dangerous.

Never trust

- File name
- File extension
- MIME type

---

# Risks

Attackers may upload

- Executable files
- Malware
- Huge files
- Fake image files
- Scripts

---

# Safe File Upload Practices

- Validate file type.
- Validate file size.
- Generate server-side filenames.
- Store uploads outside executable directories.
- Scan uploads if required.
- Limit upload size.

---

# Clickjacking

An attacker loads your website inside an invisible iframe.

Users unknowingly click buttons.

Possible consequences

- Money transfers
- Account changes
- Settings modification

---

# Preventing Clickjacking

Use security headers such as

```text
X-Frame-Options
```

or

```text
Content-Security-Policy
```

to prevent embedding.

---

# Security Headers

Useful HTTP response headers

- Content-Security-Policy
- X-Frame-Options
- X-Content-Type-Options
- Referrer-Policy
- Strict-Transport-Security (HSTS)

These provide additional browser-side protection.

---

# Browser Security Checklist

✅ Escape HTML.

✅ Sanitize user content.

✅ Enable CSP.

✅ Use HttpOnly cookies.

✅ Use Secure cookies.

✅ Use SameSite.

✅ Protect against CSRF.

✅ Configure CORS correctly.

✅ Validate uploaded files.

---

# Best Practices

- Never trust browser input.
- Escape everything rendered into HTML.
- Keep authentication cookies protected.
- Verify every state-changing request.
- Restrict cross-origin access.
- Never execute uploaded files.
- Use security headers.

---

# Interview Takeaways

Know

- XSS
- Stored vs Reflected vs DOM XSS
- CSP
- HttpOnly
- Secure Cookie
- SameSite
- CSRF
- CSRF Tokens
- CORS
- File Upload Security
- Clickjacking
- Security Headers

---

# Mental Model

```text
User Input

↓

Validate

↓

Escape / Sanitize

↓

Browser

↓

Security Headers

↓

Protected User
```

**Golden Rule**

> **Anything rendered by a browser can become an attack vector. Treat every piece of user content as potentially malicious until it is safely escaped or sanitized.**

# LEC 15D - Backend Security (Part 4) - Production Security & Best Practices

# Authorization Security

Authentication answers

```text
Who are you?
```

Authorization answers

```text
What are you allowed to do?
```

Authentication without authorization is insecure.

---

# Never Trust Client Decisions

The frontend is **not** a security boundary.

Never trust

- Hidden buttons
- Disabled UI
- Hidden fields
- User roles sent by the client

Always verify permissions on the backend.

---

# Principle of Least Privilege

Every user,

service,

or application

should have **only the permissions it absolutely needs**.

Examples

- Read-only database users
- Limited API permissions
- Minimum cloud permissions
- Restricted admin access

Smaller permissions mean smaller damage if compromised.

---

# Input Validation

Every external input should be validated.

Examples

- Email format
- String length
- Numeric ranges
- Enum values
- Required fields
- File types
- File sizes

Validation improves both security and reliability.

---

# Validation Layers

Never rely on only one layer.

```text
Frontend Validation

↓

Backend Validation

↓

Database Constraints
```

Each layer protects against different failures.

---

# Output Encoding

Input validation protects incoming data.

Output encoding protects users.

Whenever data is displayed,

encode it according to the destination

- HTML
- JavaScript
- URL
- JSON

Wrong encoding creates XSS vulnerabilities.

---

# Secrets Management

Never hardcode

- Database passwords
- API keys
- JWT secrets
- OAuth secrets
- Cloud credentials

Use

- Environment variables
- Secret managers
- Vault services

---

# Secret Rotation

Secrets should not remain unchanged forever.

Good systems support

- Secret rotation
- Revoking old secrets
- Replacing compromised credentials

---

# HTTPS Everywhere

Sensitive data should always travel through encrypted connections.

Benefits

- Privacy
- Integrity
- Authentication

Never expose authentication over plain HTTP.

---

# Rate Limiting

Rate limiting prevents abuse.

Examples

- Login attempts
- OTP requests
- Password reset
- Public APIs
- File uploads

---

# Why Rate Limiting?

Protects against

- Brute-force attacks
- Credential stuffing
- API abuse
- Denial-of-Service attempts

---

# Common Rate Limit Strategies

- Requests per second
- Requests per minute
- Per-user limits
- Per-IP limits
- Sliding window
- Token bucket

---

# Account Lockout

After repeated failed logins

```text
Too Many Attempts

↓

Temporary Lock

↓

Retry Later
```

Helps reduce password guessing attacks.

---

# Logging Security Events

Log important events

- Login
- Logout
- Failed login
- Permission denied
- Password reset
- Token revocation
- Admin actions

Avoid logging

- Passwords
- Tokens
- Secrets
- Credit card numbers

---

# Monitoring

Watch for

- Unusual login activity
- Sudden traffic spikes
- Repeated failures
- High error rates
- Suspicious API usage

Monitoring helps detect attacks early.

---

# Dependency Security

Modern applications use hundreds of third-party libraries.

A vulnerable dependency can compromise your application.

Best practices

- Update dependencies regularly.
- Remove unused packages.
- Monitor known vulnerabilities.
- Prefer trusted libraries.

---

# Secure Defaults

Applications should be secure

even if developers forget configuration.

Examples

- HTTPS enabled
- Secure cookies
- Authentication required
- Least privilege
- Safe CORS defaults

---

# Defense in Depth

Never rely on one security layer.

Example

```text
Validation

↓

Authentication

↓

Authorization

↓

Rate Limiting

↓

Logging

↓

Monitoring
```

If one layer fails,

others still provide protection.

---

# Common Security Mistakes

❌ Trusting frontend validation

❌ Building SQL with string concatenation

❌ Weak password hashing

❌ Hardcoded secrets

❌ Missing authorization checks

❌ Running with administrator permissions

❌ Unlimited login attempts

❌ Logging sensitive information

❌ Ignoring dependency updates

---

# Security Checklist Before Deployment

Authentication

- Passwords hashed
- Sessions secure
- Tokens protected

Authorization

- Backend permission checks
- Least privilege

Input

- Validation
- Sanitization
- Parameterized queries

Browser

- CSP
- Secure cookies
- SameSite
- CSRF protection

Infrastructure

- HTTPS
- Secrets managed securely
- Logging enabled
- Monitoring enabled
- Rate limiting configured

---

# Backend Security Mental Model

Whenever writing backend code,

ask yourself

```text
Can this input be modified?

↓

Can this command be abused?

↓

Can this user access something they shouldn't?

↓

What happens if this secret leaks?

↓

How do I limit the damage?
```

Thinking this way prevents many vulnerabilities before they exist.

---

# OWASP Mindset

Many common vulnerabilities fall into categories such as

- Injection
- Broken Authentication
- Broken Authorization
- Security Misconfiguration
- Sensitive Data Exposure

Learning the OWASP Top 10 is highly recommended for every backend engineer.

---

# Best Practices

- Never trust user input.
- Validate every request.
- Escape every browser output.
- Use parameterized queries.
- Hash passwords with bcrypt/Argon2.
- Enforce backend authorization.
- Protect secrets.
- Enable HTTPS.
- Apply least privilege.
- Rate-limit sensitive endpoints.
- Log security events.
- Keep dependencies updated.
- Layer multiple security mechanisms.

---

# Interview Takeaways

Know

- Authentication vs Authorization
- Least Privilege
- Defense in Depth
- Rate Limiting
- Secure Secrets
- HTTPS
- Secure Logging
- Dependency Security
- Input Validation
- Output Encoding
- OWASP Top 10

---

# Complete Backend Security Flow

```text
Request

↓

Validation

↓

Authentication

↓

Authorization

↓

Business Logic

↓

Parameterized Queries

↓

Database

↓

Response Encoding

↓

Browser

↓

Logging & Monitoring
```

---

# Final Mental Model

A secure backend never assumes

- The client is honest.
- Input is safe.
- Requests are valid.
- Users behave correctly.
- Infrastructure is perfect.

Instead, it verifies every assumption before trusting it.

---

# Golden Rules

1. Never trust user input.
2. Separate code from data.
3. Authenticate every user.
4. Authorize every action.
5. Store only hashed passwords.
6. Use least privilege everywhere.
7. Protect secrets.
8. Encrypt communication.
9. Monitor continuously.
10. Assume attackers will try every edge case.

---

# Security Summary

A secure backend is built by combining many small protections rather than relying on a single mechanism.

```text
Validate

↓

Authenticate

↓

Authorize

↓

Execute Safely

↓

Log

↓

Monitor

↓

Respond
```

Every layer reduces risk, and together they create a resilient system.
