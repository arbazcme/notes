# Middleware

* Function that runs between request and route handler.
* Receives (req, res, next).

## Middleware can:

1. Read/modify req object.
2. Read/modify res object.
3. End request-response cycle.
4. Pass control using next().

## Flow

```text
Client
   ↓
Middleware(s)
   ↓
Route Handler
   ↓
Response
```

## Common Uses

* Authentication
* Authorization
* Logging
* Validation
* Parsing request body (express.json())
* Error handling
* Rate limiting
* CORS
* Caching/Performance

## Key Points

* Middleware runs in registration order.
* next() passes control to next middleware/route.
* res.send()/res.json() can end the cycle.
* If neither next() nor a response is sent, request hangs.
* Middleware improves security, organization, reusability, and maintainability.

---

# HTTP Status Codes

## Categories

| Range | Meaning      |
| ----- | ------------ |
| 1xx   | Information  |
| 2xx   | Success      |
| 3xx   | Redirect     |
| 4xx   | Client Error |
| 5xx   | Server Error |

## Common Codes

### 200 OK

* Request successful

### 201 Created

* New resource created

### 204 No Content

* Success, nothing returned

### 400 Bad Request

* Invalid request data

### 401 Unauthorized

* Missing/invalid authentication

### 403 Forbidden

* Authenticated but lacks permission

### 404 Not Found

* Resource doesn't exist

### 409 Conflict

* Duplicate/conflicting resource

### 422 Unprocessable Content

* Validation/business-rule failure

### 429 Too Many Requests

* Rate limit exceeded

### 500 Internal Server Error

* Unexpected server failure

### 503 Service Unavailable

* Maintenance/overloaded

### 504 Gateway Timeout

* Upstream service timed out

## Memory Trick

```text
401 = Who are you?
403 = I know you, but you can't do that.
404 = I can't find it.
500 = I broke it.
```
