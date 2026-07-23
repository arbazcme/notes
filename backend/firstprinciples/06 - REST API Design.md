# 06 - REST API Design (Part 1)

## Goal

A good API should be:

- Easy to understand
- Predictable
- Consistent
- Standardized
- Easy to integrate
- Easy to maintain

Follow REST conventions so consumers of your API don't have to guess its behavior.

---

# What is REST?

REST (Representational State Transfer)

is an architectural style for designing APIs.

It defines **guidelines**, not strict rules.

Goal:

- Scalability
- Simplicity
- Consistency

---

# Why REST?

Without standards:

- Different API styles everywhere
- Hard to integrate
- Confusing routes
- Different status codes
- More bugs

REST solves this by making APIs predictable.

---

# REST Constraints

## 1. Client-Server

Separate responsibilities.

```text
Frontend

↓

HTTP

↓

Backend
```

Frontend
- UI
- User interaction

Backend
- Business Logic
- Database

---

## 2. Stateless

Server remembers nothing about previous request.

Every request must contain everything required.

Example

```text
Authorization Token

User ID

Request Body
```

must be sent every request.

Benefits

- Easy scaling
- Any server can handle request
- Load Balancer friendly

---

## 3. Cacheable

Server tells client whether response can be cached.

Benefits

- Faster responses
- Less server load

---

## 4. Layered System

Client doesn't know whether request goes through

```text
Load Balancer

↓

API Gateway

↓

Proxy

↓

Backend
```

Each layer only knows the next layer.

---

## 5. Uniform Interface

Every API should follow the same style.

Examples

```text
GET /books

POST /books

PATCH /books/1

DELETE /books/1
```

instead of random naming.

---

## 6. Code on Demand (Optional)

Server may send executable code (JavaScript).

Rarely discussed in backend interviews.

---

# Why is it called REST?

REST =

Representational

State

Transfer

---

## Representation

A resource can be represented in multiple formats.

Example

User

↓

JSON

↓

HTML

↓

XML

Same resource.

Different representation.

---

## State

Current data of resource.

Example

Shopping Cart

```text
Items

Quantity

Price
```

This is the current state.

---

## Transfer

Client and Server exchange resource representations through HTTP.

```text
Client

⇄

HTTP

⇄

Server
```

---

# API URL Structure

```text
https://api.example.com/v1/books?sort=name
```

Breakdown

```text
https://

Scheme

↓

api.example.com

Domain

↓

v1

Version

↓

books

Resource

↓

?sort=name

Query Parameters
```

---

# API Best Practices

## Use API subdomain

```text
api.example.com
```

---

## Version APIs

```text
/v1

/v2
```

Allows future changes without breaking clients.

---

## Resource names are plural

✅

```text
/books

/users

/projects
```

❌

```text
/book

/user
```

Even for fetching one resource.

Correct

```text
/books/1
```

---

## Resource Hierarchy

Each '/'

represents hierarchy.

Example

```text
/books/12
```

Meaning

```text
Books Collection

↓

Book 12
```

---

# Slugs

Human-readable identifier.

Example

Book Name

```text
Harry Potter
```

Slug

```text
harry-potter
```

Rules

- lowercase
- hyphens
- no spaces
- no underscores

---

# HTTP Methods

## GET

Purpose

Fetch resource.

Example

```text
GET /books
```

---

## POST

Purpose

Create resource.

Example

```text
POST /books
```

---

## PUT

Replace entire resource.

Client sends complete object.

Example

```text
PUT /users/10
```

Replace whole user.

---

## PATCH

Update only required fields.

Example

```text
PATCH /users/10

{
    "name":"Alex"
}
```

Only name changes.

---

## DELETE

Delete resource.

Example

```text
DELETE /users/10
```

---

# Idempotency

Meaning

Calling an API multiple times

produces the same final state.

---

## GET ✅

```text
GET /books
```

100 calls

↓

No server data changes.

Idempotent.

---

## PUT ✅

```text
PUT

name = Bob
```

100 calls

↓

Still

```text
name = Bob
```

Same final state.

---

## PATCH ✅

```text
PATCH

name = Bob
```

100 calls

↓

Still Bob.

---

## DELETE ✅

First call

↓

Deletes resource.

Remaining calls

↓

Nothing changes.

Still deleted.

---

## POST ❌

```text
POST /books
```

Every request creates

new resource.

Different side effects.

Not idempotent.

---

# POST for Custom Actions

If action is not CRUD,

use POST.

Example

```text
POST /emails/send

POST /payments/refund

POST /orders/checkout
```

These are actions,

not resources.

---

# API Design Workflow

Never start coding first.

Correct order

```text
Requirements

↓

UI / Wireframes

↓

Resources

↓

Database Schema

↓

API Design

↓

Implementation
```

---

# Identify Resources

Resources are usually nouns.

Example

Project Management App

Resources

- Users
- Organizations
- Projects
- Tasks
- Tags

These become database tables and API endpoints.

---

# CRUD APIs

Every resource usually has

```text
Create

Read

Update

Delete
```

Example

```text
POST   /projects

GET    /projects

GET    /projects/{id}

PATCH  /projects/{id}

DELETE /projects/{id}
```

---

# Create API

```text
POST /organizations
```

Request

```json
{
  "name":"ABC",
  "status":"active",
  "description":"..."
}
```

Server generates

- id
- createdAt
- updatedAt

Client should not send these.

Success

```text
201 Created
```

Return newly created object.

---

# List API

```text
GET /organizations
```

Returns

```json
[
   ...
]
```

Status

```text
200 OK
```

---

# Golden Rules

- Design API before implementation.
- Follow REST standards.
- Use plural resource names.
- Version APIs.
- Use nouns in URLs.
- Use HTTP methods correctly.
- Use PATCH for partial updates.
- Use PUT for full replacement.
- POST creates resources or performs custom actions.
- GET never modifies server state.
- Keep URLs simple and predictable.
- Build APIs around resources, not database queries.


# REST API Design (Part 2)

# Designing Resources

Think in terms of

```text
Resources

NOT

Database Tables
```

Ask

> "What objects exist in my system?"

Example

```text
Project Management App

Organizations

Projects

Tasks

Users

Tags
```

Each resource usually gets its own API.

---

# CRUD Pattern

Every resource usually exposes

```text
POST

GET (List)

GET (Single)

PATCH

DELETE
```

Example

```text
POST   /projects

GET    /projects

GET    /projects/{id}

PATCH  /projects/{id}

DELETE /projects/{id}
```

---

# Nested Resources

Use nested routes only when child depends on parent.

Example

Project

↓

Tasks

```text
/projects/{projectId}/tasks
```

Good.

Because

Task belongs to Project.

---

Avoid unnecessary nesting.

Bad

```text
/users/1/projects/2/tasks/8/comments/5
```

Too deep.

Try to keep nesting

≤ 2–3 levels.

---

# Path Parameters

Used to identify a specific resource.

Example

```text
/books/10
```

Here

```text
10
```

is Path Parameter.

---

# Query Parameters

Used for

- Filtering
- Searching
- Sorting
- Pagination

Examples

```text
/books?page=2

/books?limit=20

/books?sort=name

/books?search=harry
```

Do NOT use query parameters

to identify resources.

Wrong

```text
/books?id=10
```

Correct

```text
/books/10
```

---

# Filtering

Example

```text
/books?author=rowling
```

Returns

only Rowling books.

---

# Sorting

Example

```text
/books?sort=name

/books?sort=createdAt
```

Descending

```text
/books?sort=-createdAt
```

(Common convention.)

---

# Pagination

Never return

100000 records.

Instead

```text
/books?page=2&limit=20
```

or

```text
/books?offset=20&limit=20
```

Benefits

- Faster
- Less memory
- Less bandwidth

---

# Search

Example

```text
/books?search=harry
```

Searches matching books.

Keep search

separate from filtering.

---

# Success Responses

Always return consistent structure.

Example

```json
{
  "data": ...
}
```

or

```json
{
  "success": true,
  "data": ...
}
```

Be consistent across APIs.

---

# Error Responses

Don't return random strings.

Use structured responses.

Example

```json
{
  "message":"Book not found",
  "code":"BOOK_NOT_FOUND"
}
```

Benefits

- Easy debugging
- Frontend can react based on code
- Consistent API

---

# Common Status Codes

## Success

```text
200 OK
```

Successful GET

---

```text
201 Created
```

Resource created.

---

```text
204 No Content
```

Successful

DELETE

No response body.

---

# Client Errors

```text
400 Bad Request
```

Invalid input.

---

```text
401 Unauthorized
```

Authentication missing/invalid.

---

```text
403 Forbidden
```

Authenticated

but

not allowed.

---

```text
404 Not Found
```

Resource doesn't exist.

---

```text
409 Conflict
```

Duplicate resource

or

business conflict.

---

```text
422 Unprocessable Entity
```

Input format correct

but

business validation failed.

Example

Negative quantity.

---

# Server Errors

```text
500 Internal Server Error
```

Unexpected backend error.

---

# Response Consistency

Every endpoint

should follow similar response style.

Good

```json
{
  "data": {...}
}
```

instead of

sometimes

```json
{
 "user":...
}
```

and elsewhere

```json
{
 "book":...
}
```

---

# Naming Rules

Use

lowercase

```text
/users
```

Use

hyphens

```text
/user-profile
```

Avoid

```text
UserProfile

user_profile

USERPROFILE
```

---

# URL Should Represent Resource

Good

```text
/users

/orders

/books
```

Bad

```text
/FetchUsers

/GetBooks

/DeleteUser
```

HTTP Method already tells action.

---

Correct

```text
GET /users
```

NOT

```text
GET /getUsers
```

---

# Request Body

Client sends only editable fields.

Do NOT send

```text
id

createdAt

updatedAt
```

Server generates them.

---

# Think Like Consumer

While designing API ask

"If someone sees only the API,

without my code,

can they understand it?"

If yes

your API is good.

---

# API Design Checklist

✅ Resources are nouns

✅ Plural names

✅ Correct HTTP methods

✅ Versioning

✅ Path params for IDs

✅ Query params for filtering

✅ Pagination

✅ Consistent responses

✅ Proper status codes

✅ Predictable URLs

---

# Interview Takeaways

- REST is about resources.
- URLs contain nouns, not actions.
- HTTP method defines the action.
- GET → Fetch
- POST → Create / Custom Action
- PATCH → Partial Update
- PUT → Full Replace
- DELETE → Delete
- Path Parameters identify resources.
- Query Parameters modify retrieval.
- Keep APIs predictable and consistent.
- Always design APIs before implementation.
