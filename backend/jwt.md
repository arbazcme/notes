# 🔐 JWT (JSON Web Token) Security Mechanics

> **TL;DR:** JWTs are used for *proving* who you are, not for *hiding* data. They are digitally signed, but they are NOT encrypted.

## 🧩 The 3 Parts of a JWT
A JWT is a single string separated by dots: `Header.Payload.Signature`

1. **Header:** Tells the server what algorithm was used (usually `HS256`).
2. **Payload:** The actual JSON data (User ID, Role, Expiration). **This is public.**
3. **Signature:** The security seal. It is created by taking the Header + Payload and hashing it using your server's highly secure `SECRET_KEY`.

---

## ⚠️ The Golden Rule of JWTs
**Anyone can read a JWT. Only the server can create or verify a JWT.**

Because the payload is merely encoded (Base64Url) and not encrypted, anyone who intercepts the token can paste it into `jwt.io` and read the exact contents. 
* ❌ **Never** store passwords, credit cards, or sensitive data in a JWT.
* ✅ **Only** store identifiers (like `userId`) and authorization flags (like `role: "admin"`).

---

## 🕵️‍♂️ The Tampering Scenario (Why it is secure)
If the payload is public, what stops a regular user from taking their token, going to `jwt.io`, changing `"role": "user"` to `"role": "admin"`, and sending it back to the server?

**The Signature prevents this.**

Here is the exact server-side flow when a token is received:
1. The server receives the token: `Header.TamperedPayload.OriginalSignature`.
2. The server ignores the attached signature for a moment.
3. It takes the `TamperedPayload` and runs it through the hashing algorithm using its own private `SECRET_KEY`.
4. It compares its *newly calculated signature* against the *OriginalSignature* attached to the token.
5. Because the payload data was changed, the math results in a completely different string. **The signatures do not match.**
6. The server instantly knows the token was tampered with and rejects the request (`403 Forbidden`).

**Conclusion:** You cannot alter the data in a JWT without generating a new signature, and you cannot generate a valid signature without knowing the server's private `SECRET_KEY`.
