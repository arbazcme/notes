# Redis

## What is Redis?

Redis is an **in-memory database** used for extremely fast data access.

- Stores data in **RAM**
- Very fast read/write operations
- Commonly used for **hot records** (frequently accessed data)
- Acts as a layer between the application server and the main database

### Why Redis?

Instead of hitting the database for every request:

1. Application checks Redis first.
2. If data exists → **Cache Hit** ✅
3. If data does not exist → **Cache Miss** ❌
4. Fetch from database and store the result in Redis for future requests.

---

## Common Redis Use Cases

### 1. Caching

Store frequently accessed records in Redis to reduce database load.

Example:
- User profiles
- Product details
- Trending posts

### 2. Session Storage

Store user session information.

Examples:
- Logged-in user state
- Authentication data
- Shopping cart information

### 3. OTP Storage

Store OTPs with expiration times.

Example:
- OTP valid for 5 minutes
- Automatically removed after expiry

### 4. Rate Limiting

Prevent excessive requests from a user.

Examples:
- Login attempts
- OTP requests
- API request throttling

Can implement:
- Cooldown periods
- Request limits per minute/hour

### 5. Job Queues

Store and process background jobs asynchronously.

Examples:
- Sending emails
- Image processing
- Notifications
- Report generation

---

# CORS (Cross-Origin Resource Sharing)

## 1. Cookies Are Automatic

Browsers automatically attach cookies to requests based on the destination domain.

The browser does **not** check who triggered the request.

Example:

If a user is logged into:

```text
mybank.com
```

and visits:

```text
evil-hacker.com
```

the browser may still attach the bank's cookies when a request is made to the bank's server.

This automatic behavior creates security risks.

---
