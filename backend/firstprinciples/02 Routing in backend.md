# Routing in Backend


## Routing

### What is Routing?

Routing decides **where a request should go** inside the backend.

When a client sends a request, the server looks at:

- **HTTP Method** → What action?
- **URL Path** → Which resource?

Together, they identify the correct handler (function) to execute.

```
Client
   │
GET /users
   │
   ▼
Server Router
   │
Matches:
GET + /users
   │
   ▼
getUsers() Handler
   │
   ▼
Response
```

### Example

```
GET /users
```

- Method = `GET` → Fetch data
- Path = `/users` → Users resource

The router maps it to:

```js
getUsers()
```

Similarly,

```
POST /users
```

goes to

```js
createUser()
```

Even though the path is the same, the **HTTP method is different**, so the router calls a different handler.

---

# Static Routes

A **static route** has a **fixed URL**.

Nothing inside the path changes.

## Examples

```
GET  /users
POST /users
GET  /products
DELETE /orders
```

Here,

```
/users
```

will always remain

```
/users
```

There are **no variable values** in the path.

---

# Dynamic Routes

Sometimes we need information about **one specific resource**.

Example:

```
Fetch user 42
Fetch user 105
Fetch user 999
```

Creating routes like

```
/users42
/users105
/users999
```

would be impossible.

Instead we use **dynamic routes**.

```
GET /users/:id
```

Here,

```
:id
```

is a **path parameter**.

It can become anything.

Examples:

```
GET /users/1
GET /users/15
GET /users/500
```

The router extracts the value.

```
GET /users/500

↓

id = 500

↓

Find user 500
```

Example (Express):

```js
app.get("/users/:id", (req, res) => {
    console.log(req.params.id);
});
```

Request:

```
GET /users/123
```

Output:

```
123
```

---

# Path Parameters

A **path parameter** is the **variable part of the URL path**.

It identifies **which resource** the request is about.

General form

```
/users/:id
/products/:productId
/books/:bookId
```

Examples

```
GET /users/15
```

```
id = 15
```

```
GET /products/88
```

```
productId = 88
```

### Rule

Path parameters answer:

> **Which specific resource?**

Examples

```
/users/15
```

Specific user.

```
/orders/91
```

Specific order.

```
/posts/8
```

Specific post.

---

# Query Parameters

Query parameters send **extra information** to the server.

They are **not part of the resource itself**.

Instead, they modify **how data is returned**.

They start after a

```
?
```

General form

```
/path?key=value
```

Examples

```
/books?page=2
```

```
page = 2
```

---

```
/users?sort=name
```

```
sort = name
```

---

```
/products?category=mobile
```

```
category = mobile
```

Multiple query parameters

```
/products?page=2&sort=price&category=mobile
```

Meaning

```
page = 2

sort = price

category = mobile
```

Example (Express)

```js
app.get("/books", (req, res) => {
    console.log(req.query.page);
});
```

Request

```
GET /books?page=3
```

Output

```
3
```

### Common Uses

- Pagination

```
/books?page=2
```

- Searching

```
/search?q=nodejs
```

- Filtering

```
/products?category=laptop
```

- Sorting

```
/users?sort=age
```

### Difference

```
Path Parameter

/users/12

↓

Specific resource
```

```
Query Parameter

/users?page=2

↓

Extra options
```

---

# Nested Routes

Nested routes represent **relationships between resources**.

Instead of one resource,

they describe **a resource inside another resource**.

Example

```
/users/123/posts/456
```

Breakdown

```
users
```

↓

Users collection

```
123
```

↓

Specific user

```
posts
```

↓

Posts belonging to that user

```
456
```

↓

Specific post

Visualization

```
users
   │
   └──123
        │
        └──posts
              │
              └──456
```

Meaning

> Get post **456** that belongs to user **123**.

Another example

```
/courses/5/students/17
```

Meaning

Student **17**

inside

Course **5**

Nested routes make APIs more meaningful and organized.

---

# Route Versioning

APIs change over time.

Sometimes responses change.

Example

Old response

```json
{
    "name": "John"
}
```

New response

```json
{
    "firstName": "John"
}
```

This would break older applications.

Instead of replacing the old API,

we create a new version.

```
/api/v1/users
```

Old version

```
/api/v2/users
```

New version

Both versions can exist together.

```
Client A
      │
      ▼
/api/v1/users
```

```
Client B
      │
      ▼
/api/v2/users
```

Old applications continue working,

while new applications use the updated API.

Eventually,

```
v1
```

can be **deprecated** (marked for removal),

and later deleted.

---

# Catch-All Routes

A catch-all route handles **every request that doesn't match any defined route**.

Example

Defined routes

```
GET /users

POST /users

GET /products
```

User requests

```
GET /banana
```

No route exists.

Instead of crashing,

the catch-all route runs.

Example (Express)

```js
app.use("*", (req, res) => {
    res.status(404).send("Route not found");
});
```

Flow

```
Incoming Request
        │
        ▼
Does route exist?

      Yes ─────► Execute Handler

       No
        │
        ▼
Catch-All Route
        │
        ▼
404 Route Not Found
```

This provides a proper error message instead of an empty or confusing response.

---

# Summary

| Concept | Purpose | Example |
|---------|---------|---------|
| Routing | Maps a request to the correct handler | `GET /users` |
| Static Route | Fixed URL path | `/users` |
| Dynamic Route | URL contains variables | `/users/:id` |
| Path Parameter | Identifies a specific resource | `/users/25` |
| Query Parameter | Sends extra options (filter, sort, page) | `/users?page=2` |
| Nested Route | Represents relationships between resources | `/users/1/posts/5` |
| Route Versioning | Maintain multiple API versions | `/api/v1/users` |
| Catch-All Route | Handles unknown routes | `*` → 404 Not Found |
