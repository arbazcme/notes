# 05 - Controllers, Services, Repositories, Middleware & Request Context

## Goal

A backend is split into layers so that each layer has **one responsibility**.

Benefits:
- Cleaner code
- Easier debugging
- Easier testing
- Easier scaling
- Easier to add new features

---

# Complete Request Flow

```text
Client
   │
HTTP Request
   │
Entry Point
   │
Middleware(s)
   │
Routing
   │
Middleware(s)
   │
Controller / Handler
   │
Service
   │
Repository
   │
Database
   │
Repository
   │
Service
   │
Controller
   │
Response
   │
Client
```

---

# 1. Controller (Handler)

## Responsibility

Handles everything related to **HTTP**.

Receives:

- Request
- Response

Responsibilities:

- Read request data
- Deserialize request (if required)
- Validate input
- Transform input
- Call service
- Decide HTTP status code
- Send response

Controller should **NOT** contain business logic.

Think:

> Controller = HTTP Manager

---

## Controller Workflow

```text
Receive Request

↓

Extract Data

↓

Deserialize

↓

Validate

↓

Transform

↓

Call Service

↓

Receive Result

↓

Return HTTP Response
```

---

## Extract Data

Depending on request type:

GET
- Query Parameters
- Path Parameters

POST
PUT
PATCH
DELETE

- Request Body

---

## Deserialization

Client sends

```text
JSON
```

Backend converts JSON into native objects.

Examples:

Node.js

```text
JSON → JavaScript Object
```

Go

```text
JSON → Struct
```

Python

```text
JSON → Dictionary / Class
```

If deserialization fails

```text
400 Bad Request
```

---

## Validation

Validate **everything** coming from client.

Examples

- Required fields
- Data types
- Length
- Range
- Email format
- Allowed values

Invalid input

```text
400 Bad Request
```

---

## Transformation

After validation,

modify data into a format convenient for backend.

Example

Client

```text
GET /books
```

No sort parameter.

Transformation

```text
sort = "date"
```

instead of leaving it empty.

Purpose:

- Set defaults
- Normalize data
- Reduce conditions later

---

# 2. Service Layer

## Responsibility

Contains the **actual business logic**.

Think:

> Brain of backend.

Examples:

- Register user
- Login
- Place order
- Calculate price
- Send email
- Call payment gateway
- Combine multiple database queries

Service should know nothing about:

- HTTP
- Status Codes
- Request
- Response

It simply

```text
Input

↓

Business Logic

↓

Output
```

---

Service can:

- Call repositories
- Call external APIs
- Send notifications
- Send emails
- Merge data
- Perform calculations

---

# 3. Repository Layer

## Responsibility

Only communicates with database.

Think:

> Database Manager

Responsibilities

- SELECT
- INSERT
- UPDATE
- DELETE

Nothing else.

---

Repository should:

Input

↓

Build SQL Query

↓

Execute Query

↓

Return Result

---

Repository should have one responsibility.

Good

```text
getBookById()

getAllBooks()

insertBook()
```

Bad

```text
getBooks(optionalId)
```

because one function performs multiple jobs.

---

# Responsibility Summary

| Layer | Responsibility |
|-------|----------------|
| Controller | HTTP handling |
| Service | Business Logic |
| Repository | Database Operations |

---

# Why Separate Them?

Without layers

```text
One huge function
```

Problems:

- Hard to debug
- Hard to test
- Hard to maintain
- Hard to reuse

With layers

```text
Controller

↓

Service

↓

Repository
```

Each layer has one job.

---

# Middleware

Middleware is a function executed

**before**, **between**, or **after**

request processing.

```text
Request

↓

Middleware

↓

Routing

↓

Middleware

↓

Controller

↓

Response
```

---

Middleware receives

- Request
- Response
- next()

---

## next()

Moves execution to next stage.

```text
Middleware 1

↓

next()

↓

Middleware 2

↓

next()

↓

Controller
```

Without calling `next()`

request stops there.

---

Middleware can

- Read request
- Modify request
- Modify response
- Send response immediately
- Stop request
- Pass request forward

---

# Why Middleware?

Avoid writing common logic inside every controller.

Instead of

```text
Controller A

Authentication

Controller B

Authentication

Controller C

Authentication
```

Write once

```text
Authentication Middleware
```

and reuse everywhere.

---

# Common Middleware

## CORS

Checks request origin.

Adds CORS headers.

Runs for every request.

---

## Authentication

- Read token
- Verify token
- Extract user information

Failure

```text
401 Unauthorized
```

Success

Store

- User ID
- Role
- Permissions

inside Request Context.

---

## Rate Limiting

Prevents abuse.

Example

```text
30 requests

per

2 seconds
```

Exceeded

```text
429 Too Many Requests
```

---

## Logging

Logs

- URL
- Method
- Headers
- Query
- Time
- Status

Useful for debugging and monitoring.

---

## Global Error Handler

Catches errors from anywhere.

Converts them into proper responses.

Client Error

```text
4xx
```

Server Error

```text
5xx
```

Usually placed **last**.

---

## Compression

Compress response before sending.

Benefits

- Smaller response
- Faster network transfer

---

## Serialization / Validation Middleware

Some frameworks move

- JSON Parsing
- Validation
- Transformation

into middleware instead of controller.

---

# Middleware Order Matters

Typical order

```text
Request

↓

CORS

↓

Logging

↓

Authentication

↓

Rate Limiting

↓

Routing

↓

Controller

↓

Global Error Handler

↓

Response
```

Incorrect ordering can make security and error handling fail.

---

# Request Context

Every request has its own private storage.

Think of it as

```text
Request Backpack
```

Every middleware and controller can access it.

---

Stores information like

- User ID
- Role
- Permissions
- Request ID
- Trace ID
- Custom values

---

Example

Authentication Middleware

↓

Verify JWT

↓

Store

```text
userId

role
```

inside Request Context.

↓

Controller

↓

Read userId

↓

Insert into database

---

Never trust

```text
userId
```

coming from client.

Always use authenticated user from Request Context.

Otherwise attacker can impersonate another user.

---

# Request ID

Generate unique request ID.

Store in Request Context.

Pass it across services.

Useful for

- Debugging
- Logging
- Tracing requests

---

# Complete Backend Flow

```text
Client

↓

Middleware
(CORS, Logging, Auth...)

↓

Routing

↓

Controller
(HTTP)

↓

Service
(Business Logic)

↓

Repository
(Database)

↓

Database

↓

Repository

↓

Service

↓

Controller

↓

HTTP Response

↓

Client
```

---

# Interview Takeaways

- Controller handles HTTP only.
- Service contains business logic.
- Repository only accesses database.
- Middleware contains reusable request-processing logic.
- `next()` passes execution to the next stage.
- Middleware order is important.
- Request Context stores request-scoped data.
- Never trust user IDs from the client; use authenticated data from Request Context.
- Keep each layer focused on a single responsibility.
