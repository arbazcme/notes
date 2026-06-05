# CORS

## 1. Cookies are automatic

Your browser blindly and automatically attaches cookies to requests based only on where the request is going, without checking who actually triggered it.

---

## 2. CORS restricts at the Origin Level

Because of the dangerous automatic behavior above, CORS forces the browser to check the "Origin" (the actual website the user is currently looking at) before letting a background request happen.

---

## 3. The "Listed Sites" Allowlist

You hit the bullseye here. When an API server is set up, the developers literally write an allowlist of approved Origins.

If you build an API at api.mybank.com, you configure it to say:

> "Only accept cross-origin requests if they come from www.mybank.com or mobile.mybank.com."

If evil-hacker.com tries to trigger a request, the browser checks the API's allowlist.

Since evil-hacker.com is not on the list, the browser's CORS security guard steps in and blocks the request entirely to protect the user.
