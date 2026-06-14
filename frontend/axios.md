# Axios Quick Revision Notes

## What is Axios?

Axios is a JavaScript library used to send HTTP requests from frontend to backend.

Example:

```js
axios.get("/users");
```

---

# Creating an Axios Instance

Instead of writing:

```js
axios.get("http://localhost:5500/api/problems");
axios.post("http://localhost:5500/api/login");
```

create a reusable instance:

```js
import axios from "axios";

const api = axios.create({
  baseURL: "http://localhost:5500/api",
  headers: {
    "Content-Type": "application/json",
  },
});

export default api;
```

Now:

```js
api.get("/problems");
```

becomes:

```http
GET http://localhost:5500/api/problems
```

---

# Common Requests

## GET

```js
const response = await api.get("/problems");
```

---

## POST

```js
const response = await api.post("/auth/login", {
  username,
  password,
});
```

---

## PUT

```js
await api.put("/users/1", {
  name: "Arbaz",
});
```

---

## DELETE

```js
await api.delete("/users/1");
```

---

# Response Object

Backend:

```json
{
  "user": {
    "id": 1,
    "username": "arbaz"
  },
  "token": "abc123"
}
```

Frontend:

```js
const response = await api.post(...);
```

Response:

```js
response.data
```

contains:

```js
{
  user: {
    id: 1,
    username: "arbaz"
  },
  token: "abc123"
}
```

Destructuring:

```js
const { user, token } = response.data;
```

Equivalent:

```js
const user = response.data.user;
const token = response.data.token;
```

---

# Error Handling

```js
try {
  const response = await api.get("/problems");
} catch (error) {
  console.log(error);
}
```

Backend error:

```json
{
  "error": "Invalid credentials"
}
```

Access:

```js
error.response.data.error
```

Safe version:

```js
error.response?.data?.error
```

---

# JWT Token Storage

After login:

```js
localStorage.setItem("oj_token", token);
```

Retrieve:

```js
const token = localStorage.getItem("oj_token");
```

Delete:

```js
localStorage.removeItem("oj_token");
```

---

# Why Interceptors?

Without interceptor:

```js
const token = localStorage.getItem("oj_token");

api.get("/problems", {
  headers: {
    Authorization: `Bearer ${token}`,
  },
});
```

Need to repeat everywhere.

---

# Request Interceptor

```js
api.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem("oj_token");

    if (token) {
      config.headers["Authorization"] = `Bearer ${token}`;
    }

    return config;
  },
  (error) => Promise.reject(error)
);
```

Purpose:

* Runs before every request.
* Reads latest token.
* Automatically attaches Authorization header.

---

# Request Flow

You write:

```js
api.get("/problems");
```

Axios creates:

```js
config = {
  method: "GET",
  url: "/problems",
  headers: {},
};
```

Interceptor runs:

```js
config.headers["Authorization"] =
  "Bearer abc123";
```

Final request:

```http
GET /problems

Authorization: Bearer abc123
```

---

# Why Not Put Authorization Inside axios.create()?

Bad:

```js
const api = axios.create({
  headers: {
    Authorization:
      `Bearer ${localStorage.getItem("oj_token")}`,
  },
});
```

Reason:

* Reads token only once.
* User logs in later.
* Axios instance still holds old token.

Interceptor reads token before every request.

---

# Promise.reject(error)

```js
(error) => Promise.reject(error)
```

Means:

```text
Request preparation failed.
Pass error to catch().
```

Equivalent to:

```js
throw error;
```

inside a Promise chain.

---

# Mental Model

api.get() / api.post()
↓
Axios creates config
↓
Interceptor runs
↓
Adds JWT token
↓
Request sent
↓
Backend verifies token
↓
Response returned
↓
response.data




# Detailed Axios Request Lifecycle

## Step 1: Developer Writes

```js
api.post("/auth/login", {
  username,
  password,
});
```

---

## Step 2: Axios Creates Config Object

Internally Axios converts it into something like:

```js
config = {
  method: "POST",
  url: "/auth/login",
  data: {
    username,
    password,
  },
  headers: {},
};
```

Think:

```text
api.post(...)
      ↓
Axios builds config object
```

---

## Step 3: Request Interceptor Runs

```js
api.interceptors.request.use((config) => {
  const token = localStorage.getItem("oj_token");

  if (token) {
    config.headers["Authorization"] =
      `Bearer ${token}`;
  }

  return config;
});
```

Interceptor receives:

```js
config = {
  method: "POST",
  url: "/auth/login",
  data: {
    username,
    password,
  },
  headers: {},
};
```

---

## Step 4: Interceptor Modifies Config

After interceptor:

```js
config = {
  method: "POST",
  url: "/auth/login",
  data: {
    username,
    password,
  },
  headers: {
    Authorization: "Bearer abc123",
  },
};
```

Think:

```text
Interceptor
     ↓
Modifies config
     ↓
Returns config
```

---

## Step 5: Axios Sends Request

Final HTTP request:

```http
POST /api/auth/login

Authorization: Bearer abc123

{
  "username": "arbaz",
  "password": "123"
}
```

---

## Mental Model

```text
api.post(...)
      ↓
Axios creates config
      ↓
Interceptor modifies config
      ↓
Axios sends request
      ↓
Backend receives request
```
```
```
