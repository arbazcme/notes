##  CORS Restricts at the Origin Level

To prevent abuse, browsers enforce **CORS**.

The browser checks the request's:

```text
Origin
```

which represents the website currently being viewed by the user.

Before allowing a cross-origin request, the browser verifies whether that origin is permitted by the target server.

---

## 3. Origin Allowlist

API servers maintain an allowlist of trusted origins.

Example:

API:

```text
api.mybank.com
```

Allowed origins:

```text
www.mybank.com
mobile.mybank.com
```

Server configuration essentially says:

> Only accept cross-origin requests from approved origins.

### Allowed Request

```text
Origin: www.mybank.com
```

Result:

```text
Request Allowed ✅
```

### Blocked Request

```text
Origin: evil-hacker.com
```

Result:

```text
Request Blocked ❌
```

The browser sees that the origin is not on the server's allowlist and blocks the cross-origin request, helping protect the user from malicious websites.

---

## Quick Summary

### Redis

- In-memory database
- Stores data in RAM
- Extremely fast access
- Used for:
  - Caching
  - Session storage
  - OTP storage
  - Rate limiting
  - Job queues

### CORS

- Browsers automatically send cookies.
- CORS checks the request's Origin.
- Servers maintain an allowlist of trusted origins.
- Requests from untrusted origins are blocked by the browser.

