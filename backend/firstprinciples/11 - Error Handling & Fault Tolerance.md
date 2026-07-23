# LEC 11 - Error Handling & Fault Tolerance

# Errors are Normal

Backend applications will always encounter errors.

Examples

- Database failures
- Network failures
- External API failures
- Invalid user input
- Bugs in business logic

The goal is **not to eliminate every error**.

The goal is to **detect, contain, recover, and communicate errors gracefully**.

---

# Types of Errors

## 1. Logic Errors

The application runs successfully,

but produces the wrong result.

Example

```text
Discount applied twice

↓

Company loses money
```

These are dangerous because

- No crash occurs
- Difficult to detect
- Can silently corrupt business logic

Common causes

- Misunderstood requirements
- Incorrect algorithms
- Missing edge cases

---

## 2. Database Errors

Backend applications heavily depend on databases.

Common database failures include

### Connection Errors

Examples

- Database server down
- Network failure
- Connection pool exhausted

Result

```text
Backend

↓

Cannot access data

↓

Requests fail
```

---

### Constraint Violations

Examples

- Duplicate email (UNIQUE constraint)
- Invalid foreign key
- NOT NULL violation

These should return meaningful client errors instead of generic server errors.

---

### Query Errors

Examples

- Invalid SQL
- Wrong table name
- Syntax errors
- Query timeout

---

### Deadlocks

Two or more transactions wait on each other forever.

Database detects the deadlock and aborts one transaction.

---

## 3. External Service Errors

Modern backends depend on external systems.

Examples

- Payment gateways
- Email providers
- Authentication providers
- Redis
- Object storage
- AI APIs

Since you don't control them,

you must assume they can fail.

Common causes

- Network timeout
- DNS failure
- Authentication failure
- Service outage
- Rate limiting (HTTP 429)

---

### Handling Rate Limits

If an API returns

```text
429 Too Many Requests
```

Use

**Exponential Backoff**

```text
Retry

↓

1 sec

↓

2 sec

↓

4 sec

↓

8 sec
```

Avoid continuously retrying immediately.

---

## 4. Input Validation Errors

Users send bad data.

Examples

- Invalid email
- Missing required field
- Number out of range
- Invalid date
- Oversized input

Validate requests at the entry point.

Return

```text
HTTP 400

Bad Request
```

instead of allowing bad data into the application.

---

## 5. Configuration Errors

Examples

- Missing environment variables
- Wrong API keys
- Wrong database URL

Best practice

Validate all required configuration **before starting the server**.

Fail fast.

Never allow production to fail later because of missing configuration.

---

# Prevention Strategies

The best error handling starts **before** errors happen.

---

## Health Checks

Expose endpoints like

```text
/health
```

or

```text
/status
```

Purpose

- Verify server is running
- Used by load balancers
- Used by Kubernetes
- Used by monitoring systems

---

## Database Health Checks

Don't just check

"Server is alive."

Also verify

- Database connectivity
- Representative queries
- Query latency
- Data integrity

---

## External Service Health Checks

Regularly verify

- Payment gateway
- Email service
- Authentication provider

using safe test requests.

---

## Validate Startup Configuration

Before serving traffic

Check

- Environment variables
- Secrets
- API keys
- Database connections
- Required caches

If anything is missing

```text
Crash Immediately
```

instead of failing during user requests.

---

# Monitoring & Observability

Monitoring helps detect problems early.

Track

- HTTP errors
- Database errors
- External API failures
- Business logic failures
- Resource usage
- Response time
- Throughput

Don't monitor only failures.

Also monitor

- Latency
- CPU
- Memory
- Slow queries

Performance degradation often appears before outages.

---

# Logging

Logs help debug failures.

Prefer

- Structured logs (JSON)
- Request IDs
- Error context
- Timestamps

Avoid

- Sensitive data
- Passwords
- Tokens

---

# Recoverable vs Non-Recoverable Errors

## Recoverable

Examples

- Temporary network failure
- Email provider timeout
- Rate limit

Strategy

- Retry
- Exponential backoff
- Queue
- Background jobs

---

## Non-Recoverable

Examples

- Corrupted configuration
- Invalid request
- Missing resources

Strategy

- Fail gracefully
- Use fallback
- Disable non-critical features
- Protect important functionality

---

# Error Recovery

Possible recovery mechanisms

- Restart services
- Retry operations
- Clear corrupted cache
- Switch to backup systems
- Restore from backups
- Replay transaction logs

Always prioritize

**Data Integrity**

over availability.

---

# Error Propagation

Low-level components shouldn't always decide the final response.

Instead

```text
Repository

↓

Service

↓

Handler

↓

Global Error Handler
```

Errors move upward,

gaining more business context,

until one centralized place decides the final response.

---

# Global Error Handler

A **Global Error Handler** is the final safety net.

Every unexpected error eventually reaches it.

Responsibilities

- Log errors
- Convert internal errors into proper HTTP responses
- Hide implementation details
- Return consistent error format

Example

Validation Error

↓

```text
HTTP 400
```

Duplicate Email

↓

```text
HTTP 400
Email already exists
```

Resource Not Found

↓

```text
HTTP 404
```

Unexpected Database Failure

↓

```text
HTTP 500
```

---

# Common HTTP Status Codes

| Code | Meaning |
|------|---------|
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Resource Not Found |
| 409 | Conflict (often duplicate resource) |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

---

# Why Use a Global Error Handler?

Without one

Every controller,

service,

and repository

must handle every error individually.

This leads to

- Duplicate code
- Inconsistent responses
- Forgotten edge cases

With one

```text
Any Error

↓

Global Error Handler

↓

Consistent Response
```

Benefits

- Centralized logic
- Cleaner code
- Easier maintenance
- Better security
- Consistent API responses

---

# Best Practices

- Validate input immediately.
- Fail fast on configuration errors.
- Never expose internal error details to users.
- Retry only recoverable failures.
- Use exponential backoff.
- Monitor everything important.
- Log structured data.
- Protect data integrity.
- Handle errors centrally.
- Always return meaningful HTTP status codes.

---

# Interview Takeaways

Know

- Types of backend errors
- Recoverable vs non-recoverable errors
- Health checks
- Monitoring vs Logging
- Structured logging
- Exponential backoff
- Error propagation
- Global Error Handler
- Common HTTP status codes
- Why centralized error handling is preferred

---

# Mental Model

```text
Request

↓

Validation

↓

Business Logic

↓

Database / External Services

↓

Error?

↓

Global Error Handler

↓

Log

↓

Return Proper HTTP Response
```

**Golden Rule**

> Errors are inevitable. A good backend doesn't avoid every failure—it detects failures early, recovers when possible, and fails gracefully when necessary.
