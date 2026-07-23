# Validation & Transformation

## Why?

Before executing any business logic, **all client data must be validated and transformed**.

Purpose:
- Maintain **data integrity**
- Improve **security**
- Prevent invalid data from reaching the database
- Return meaningful client errors (`400 Bad Request`) instead of server errors (`500 Internal Server Error`)

---

# Backend Flow

```text
Client
   │
   ▼
Route
   │
   ▼
Validation & Transformation
   │
   ▼
Controller
   │
   ▼
Service
   │
   ▼
Repository
   │
   ▼
Database
```

Validation happens **after route matching** and **before the controller executes business logic**.

---

# Validation

Validation ensures the incoming data matches what the API expects.

Example:

```json
{
  "name": "John"
}
```

Requirements:

- Required field
- Correct type
- Length limits
- Format checks
- Business rules

If validation fails:

```
400 Bad Request
```

---

# Why Validate Early?

Without validation:

```text
Client
   │
Invalid Data
   │
Controller
   │
Service
   │
Repository
   │
Database Error
   │
500 Internal Server Error
```

With validation:

```text
Client
   │
Invalid Data
   │
Validation
   │
400 Bad Request
```

Never let invalid data reach the database.

---

# Types of Validation

## 1. Type Validation

Checks data type.

Examples:

- string
- number
- boolean
- array
- object

Example:

```json
{
    "age": "20"
}
```

Expected:

```json
{
    "age": 20
}
```

---

## 2. Syntactic Validation

Checks whether data follows the correct **format**.

Examples:

- Email
- Phone Number
- Date
- URL

Example:

```
abc@gmail.com ✅
abcgmail.com ❌
```

---

## 3. Semantic Validation

Checks whether the value **makes logical sense**.

Examples:

- DOB cannot be in the future.
- Age cannot be 300.
- Price cannot be negative.

Correct format alone is not enough.

---

## 4. Complex Validation

Validation involving multiple fields.

Examples:

- Password == Confirm Password
- If `married == true`, then `partnerName` is required.
- Start Date < End Date

---

# Transformation

Transformation modifies data into the format expected by the server.

Examples:

```text
"ABC@GMAIL.COM"
↓

"abc@gmail.com"
```

```
"20"
↓

20
```

```
"+91" missing

↓

Automatically prepend "+91"
```

Transformation usually happens in the same pipeline as validation.

---

# Why Transformation?

Some client data naturally arrives in an inconvenient format.

Example:

Query parameters:

```
?page=2&limit=20
```

Actually received by the server:

```text
page = "2"
limit = "20"
```

Both are strings.

Transform:

```text
"2" → 2
"20" → 20
```

Then validate:

```
number
>0
<500
```

---

# Validation vs Transformation

Validation:

```
Is this data valid?
```

Transformation:

```
Convert this data into the format we want.
```

---

# Frontend Validation vs Backend Validation

## Frontend Validation

Purpose:

- Better User Experience
- Immediate feedback
- Avoid unnecessary API calls

Example:

```
Invalid Email

↓

Show error immediately
```

---

## Backend Validation

Purpose:

- Security
- Data Integrity
- Final source of truth

Never trust client input.

Clients can bypass frontend completely using:

- Postman
- Insomnia
- curl
- Custom applications

Backend validation is **mandatory**.

---

# Rule

Frontend Validation

```
User Experience
```

Backend Validation

```
Security + Data Integrity
```

Both are required.

Frontend validation **never replaces** backend validation.

---

# Best Practices

- Validate all incoming data.
- Validate before business logic.
- Return clear validation errors (`400`).
- Never trust client input.
- Transform data before using it.
- Keep validation and transformation in one pipeline.

---

# What Should Be Validated?

Validate **every piece of data** coming from the client, including:

- Request Body (JSON)
- Query Parameters
- Path Parameters
- Headers

Never assume any client input is valid.

---

# Validation Schema

Instead of manually checking every field, define a **validation schema**.

Example:

```text
name
- required
- string
- min length = 5
- max length = 100
```

The validation pipeline compares incoming data against this schema and immediately rejects invalid requests.

---

# Layer Responsibilities

```text
Client
   │
   ▼
Validation & Transformation
   │
   ▼
Controller
   │
   ▼
Service
   │
   ▼
Repository
   │
   ▼
Database
```

### Controller

Responsible for:
- HTTP request/response handling
- Validation
- Transformation
- Calling service methods
- Returning proper HTTP status codes

### Service

Responsible for:
- Business logic
- Coordinating operations
- Calling repositories or external services

### Repository

Responsible for:
- Database operations (CRUD)
- Query execution
- Data persistence

---

# Why Separate Controller and Service?

Keeps concerns separated.

- **Controller** → HTTP-specific logic.
- **Service** → Business logic.

This makes the code cleaner, reusable, and easier to maintain.

---

# Validation & Transformation Pipeline

The pipeline runs **immediately after route matching**.

```text
Route Matched
      │
      ▼
Validation
      │
Transformation
      │
Controller
      │
Business Logic
```

Its goal is to ensure the controller always receives **clean, correctly formatted data**.

---

# Notes on Transformation

Transformation may happen:

- Before validation (e.g., `"20"` → `20`)
- After validation (e.g., normalize output)

Common transformations:

- Convert string → number
- Convert string → boolean
- Convert to lowercase
- Trim whitespace
- Add default values
- Format dates
- Normalize phone numbers

---

# Key Takeaways

- Validate **all** client inputs.
- Reject invalid requests **before** business logic.
- Return **400 Bad Request** for validation failures.
- Never trust client input.
- Backend validation is the **source of truth**.
- Frontend validation improves UX, **not security**.
- Keep validation and transformation in a single pipeline.
- Controllers handle HTTP concerns, Services handle business logic, Repositories handle database access.

