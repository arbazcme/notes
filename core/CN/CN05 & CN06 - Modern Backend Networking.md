# CN05 & CN06 - Modern Backend Networking (Final)

---

# 1. Reverse Proxy

## What is it?

A Reverse Proxy

is a server

that sits

between

clients

and

your backend servers.

```text
Client

↓

Reverse Proxy

↓

Backend Server
```

---

## Why?

Clients

never communicate

directly

with backend servers.

The Reverse Proxy

receives requests,

then forwards them.

---

## Why is it useful?

```text
Hide Backend Servers

↓

SSL/TLS Termination

↓

Load Balancing

↓

Caching

↓

Compression

↓

Security
```

Popular examples

```text
Nginx

Apache

HAProxy
```

---

# 2. Load Balancer

## What is it?

A Load Balancer

distributes

incoming requests

across

multiple servers.

---

## Why?

One server

cannot handle

millions of users.

---

## Example

```text
Users

↓

Load Balancer

↓

Server 1

↓

Server 2

↓

Server 3
```

Instead of

one server

doing all work,

many servers

share it.

---

# 3. CDN

## What is it?

CDN

(Content Delivery Network)

stores copies

of static files

around the world.

---

## Why?

If

an Indian user

needs

a CSS file,

it is faster

to download it

from India

than

from America.

---

## Example

```text
User

↓

Nearest CDN Server

↓

Image

CSS

JavaScript
```

---

## What is stored?

Mostly

```text
Images

Videos

CSS

JavaScript

Fonts
```

Not

your database.

---

# 4. Proxy vs Reverse Proxy

## Proxy

```text
Client

↓

Proxy

↓

Internet
```

Acts

for the client.

---

## Reverse Proxy

```text
Client

↓

Reverse Proxy

↓

Backend
```

Acts

for the server.

---

# 5. WebSocket

## What is it?

A WebSocket

creates

a long-lived

two-way connection.

---

## HTTP

```text
Request

↓

Response

↓

Finished
```

---

## WebSocket

```text
Connection

↓

Open

↓

Client

⇅

Server

↓

Close
```

---

## Used in

```text
Chats

Gaming

Stock Prices

Live Notifications
```

---

## Why not HTTP?

HTTP

requires

a request

before

the server

can respond.

WebSocket

lets

both sides

send data

at any time.

---

# 6. Browser → Backend (Complete Flow)

```text
Browser

↓

DNS

↓

TCP Handshake

↓

TLS Handshake

↓

HTTP Request

↓

CDN

↓

Reverse Proxy

↓

Load Balancer

↓

API Gateway (Optional)

↓

Backend Server

↓

Redis Cache

↓

Database

↓

Backend Server

↓

HTTP Response

↓

Browser
```

---

# 7. Real Backend Architecture

```text
Browser

↓

Internet

↓

CDN

↓

Reverse Proxy

↓

Load Balancer

↓

API Gateway

↓

Backend API

↓

Redis Cache

↓

Database
```

---

# 8. Redis (Networking View)

Redis

is

an in-memory database.

Instead of

asking

the database

every time,

the backend

first asks Redis.

```text
Backend

↓

Redis

↓

Found?

↓

Yes

↓

Return

------------------

No

↓

Database

↓

Store in Redis

↓

Return
```

---

# 9. Why Backend Companies Use This Architecture

```text
CDN

↓

Fast Static Files

--------------------

Reverse Proxy

↓

Security

--------------------

Load Balancer

↓

Scalability

--------------------

API Gateway

↓

Single Entry Point

--------------------

Redis

↓

Fast Reads

--------------------

Database

↓

Permanent Storage
```

---

# 10. Same-Origin Policy (SOP)

## What is it?

A browser security rule.

A webpage

can only access

resources

from

the same

Origin.

Origin means

```text
Protocol

+

Domain

+

Port
```

Example

```text
https://example.com:443
```

---

## Why?

Without SOP,

a malicious website

could read

your bank account,

emails,

or social media

opened

in another tab.

---

# 11. CORS

## What is it?

CORS

(Cross-Origin Resource Sharing)

allows

servers

to permit

requests

from

other origins.

---

## Why?

Sometimes

frontend

and backend

are hosted

on different domains.

Example

```text
Frontend

↓

https://app.com

-------------------

Backend

↓

https://api.app.com
```

The browser

asks

the backend

whether

cross-origin access

is allowed.

---

# 12. Stateless vs Stateful

## Stateless

Each request

is independent.

Example

```text
HTTP

REST APIs
```

---

## Stateful

The server

remembers

previous interactions.

Example

```text
WebSocket

Database Connection

Game Server
```

---

# 13. Idempotency

## What is it?

Calling

an API

multiple times

produces

the same result.

Example

```text
PUT /users/5
```

Updating

the same user

10 times

with identical data

still leaves

the resource

in the same state.

---

## Non-idempotent

```text
POST /orders
```

Every request

creates

a new order.

---

# 14. Polling vs Long Polling vs WebSocket

## Polling

```text
Client

↓

Request

↓

Response

↓

Wait

↓

Request Again
```

---

## Long Polling

Server

keeps

the request

open

until

new data arrives.

---

## WebSocket

One connection

remains open.

Both client

and server

can send data

any time.

---

# 15. Keep-Alive

## What is it?

Instead of

creating

a new TCP connection

for every request,

reuse

the existing one.

---

## Why?

TCP Handshake

and

TLS Handshake

are expensive.

Reusing

the connection

improves performance.

---

# 16. API Gateway

## What is it?

An API Gateway

is

a single entry point

for multiple backend services.

```text
Client

↓

API Gateway

↓

User Service

↓

Order Service

↓

Payment Service
```

---

## Why?

Clients

don't need

to know

where

every service

is located.

---

# 17. Microservices (Networking View)

Instead of

one large application,

split

the backend

into

multiple services.

```text
Client

↓

API Gateway

↓

User Service

↓

Order Service

↓

Payment Service
```

Services

communicate

over

the network

using

HTTP

or

gRPC.

---

# 18. gRPC

## What is it?

A communication protocol

used

between

backend services.

---

## Why?

Compared to

HTTP + JSON,

gRPC

is

smaller,

faster,

and

supports

streaming.

---

# 19. Common Interview Questions

Know how to explain

```text
What happens
when you type google.com?

--------------------

TCP vs UDP

--------------------

HTTP vs HTTPS

--------------------

TLS Handshake

--------------------

DNS

--------------------

Socket

--------------------

Cookies vs Sessions vs JWT

--------------------

REST API

--------------------

Reverse Proxy

--------------------

Load Balancer

--------------------

CDN

--------------------

Redis Cache

--------------------

CORS

--------------------

WebSocket

--------------------

Keep-Alive

--------------------

API Gateway

--------------------

Browser → Backend Architecture
```

---

# 20. Final Backend Architecture

```text
Browser

↓

DNS

↓

TCP Handshake

↓

TLS Handshake

↓

HTTP Request

↓

CDN

↓

Reverse Proxy

↓

Load Balancer

↓

API Gateway

↓

Backend Service

↓

Redis

↓

Database

↓

HTTP Response

↓

Browser
```
