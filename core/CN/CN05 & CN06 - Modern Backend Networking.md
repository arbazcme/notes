# CN05 & CN06 - Modern Backend Networking (Final)

# Navbar

- [1. Reverse Proxy](#1-reverse-proxy)
- [2. Load Balancer](#2-load-balancer)
- [3. CDN](#3-cdn)
- [4. Proxy vs Reverse Proxy](#4-proxy-vs-reverse-proxy)
- [5. WebSocket](#5-websocket)
- [6. Browser → Backend (Complete Flow)](#6-browser--backend-complete-flow)
- [8. Redis (Networking View)](#8-redis-networking-view)
- [9. Why Backend Companies Use This Architecture](#9-why-backend-companies-use-this-architecture)
- [10. Same-Origin Policy (SOP)](#10-same-origin-policy-sop)
- [11. CORS](#11-cors)
- [12. Stateless vs Stateful](#12-stateless-vs-stateful)
- [13. Idempotency](#13-idempotency)
- [14. Polling vs Long Polling vs WebSocket](#14-polling-vs-long-polling-vs-websocket)
- [15. Keep-Alive](#15-keep-alive)
- [16. API Gateway](#16-api-gateway)
- [17. Microservices (Networking View)](#17-microservices-networking-view)
- [18. gRPC](#18-grpc)
- [19. Common Interview Questions](#19-common-interview-questions)


## Imagine this

You visit a restaurant.

You

do NOT

go into the kitchen.

You

talk to

the waiter.

```text
Customer

↓

Waiter

↓

Kitchen
```

The waiter

takes your order,

gives it

to the kitchen,

collects the food,

and returns it

to you.

The kitchen

never talks

to customers directly.

---

## In Networking

The waiter

is the

Reverse Proxy.

The kitchen

is your

Backend Server.

```text
Client

↓

Reverse Proxy

↓

Backend Server
```

The client

sends every request

to the Reverse Proxy.

The Reverse Proxy

decides

which backend server

should handle it,

gets the response,

and sends it

back to the client.

The client

never knows

which backend server

actually processed

the request.

---

## Why use a Reverse Proxy?

### 1. Hide Backend Servers

The client

only knows

the Reverse Proxy.

Backend server IPs

remain hidden.

```text
Client

↓

Reverse Proxy

↓

Backend Servers
```

---

### 2. Load Balancing

If there are

multiple backend servers,

the Reverse Proxy

can distribute requests.

```text
Client

↓

Reverse Proxy

↙   ↓   ↘

Server 1

Server 2

Server 3
```

This prevents

one server

from becoming overloaded.

---

### 3. SSL/TLS Termination

The Reverse Proxy

handles HTTPS.

```text
Client

⇄ HTTPS

Reverse Proxy

↓

HTTP (or HTTPS)

Backend Server
```

Instead of

every backend server

handling encryption,

the Reverse Proxy

can do it once.

---

### 4. Caching

If many users

request

the same page,

the Reverse Proxy

can remember

the response.

```text
Client

↓

Reverse Proxy (Cached Copy)

↓

(No backend needed)
```

This makes

responses faster

and reduces

backend work.

---

### 5. Compression

The Reverse Proxy

can compress data

before sending it

to clients.

Smaller response

↓

Less bandwidth

↓

Faster loading

---

### 6. Security

Since

all traffic

passes through

the Reverse Proxy,

it can

- Block attackers
- Rate-limit requests
- Filter malicious traffic
- Hide backend servers

before

requests reach

your application.

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

A reverse proxy can do all of those what Load balancer does, depends on reverse proxy, if single server 
it only does caching, security,hide backend servers etc
when multiple servers can act like a load balancer as well .

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

## Proxy (Forward Proxy)

### What is it?

A Proxy

is a server

that sits

between

a client

and

the Internet.

---

### Why?

Sometimes

the client

should not

communicate

directly

with websites.

Instead,

the Proxy

receives the request,

then sends it

to the website

on behalf

of the client.

---

### How does it work?

1. Client sends request to Proxy.
2. Proxy forwards it to the website.
3. Website sends the response to the Proxy.
4. Proxy returns the response to the client.

The website

sees

the Proxy,

not

the real client.

---

### Why is it useful?

- Hide the client's IP address.
- Access restricted websites.
- Filter or monitor employee/student Internet usage.
- Cache frequently visited websites.

---

### Example

A company

blocks YouTube.

Employees

must access

the Internet

through

the company's Proxy.

The Proxy

can

allow,

block,

or monitor

websites.

---

## Reverse Proxy

### What is it?

A Reverse Proxy

is a server

that sits

between

clients

and

backend servers.

---

### Why?

Clients

should not

communicate

directly

with backend servers.

Instead,

the Reverse Proxy

receives every request,

then forwards it

to the appropriate backend server.

---


```text
Employee 1 ─┐
Employee 2 ─┼──► Company Proxy ───► Internet
Employee 3 ─┘
```
many clients can share a proxy, use of proxy is simple restricted acess via proxy

### How does it work?

1. Client sends request to the Reverse Proxy.
2. Reverse Proxy chooses a backend server.
3. Backend processes the request.
4. Reverse Proxy returns the response to the client.

The client

sees

only

the Reverse Proxy,

not

the backend servers.

---

### Why is it useful?

- Hide backend servers.
- Load balance requests.
- Handle HTTPS (SSL/TLS).
- Cache responses.
- Compress responses.
- Block malicious traffic.

---

### Example

You open

google.com.

Your request

first reaches

Google's Reverse Proxy.

It then decides

which backend server

should process

your request.

You never know

which backend server

actually handled it.

---

## Difference

### Proxy

Protects

the client.

The website

doesn't know

the real client.

---

### Reverse Proxy

Protects

the backend servers.

The client

doesn't know

the real backend server.
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
reverse proxy and load balancing is one when traffic is low , when traffic is huge the load balancer is dedicated,
 responsibility is seperate thats it !

Small/Medium systems: One Reverse Proxy does everything.

Large-scale systems: Reverse Proxy handles edge features, and a dedicated Load Balancer handles traffic distribution.

```

```text
                Client
                   │
                   ▼
          Reverse Proxy
   (HTTPS, Security, Caching)
                   │
                   ▼
             API Gateway
      (Routing, Auth, Rate Limit)
        ┌──────────┼──────────┐
        ▼          ▼          ▼
     User LB    Order LB   Payment LB
        │          │           │
     ┌──┴──┐    ┌──┴──┐     ┌──┴──┐
     ▼     ▼    ▼     ▼     ▼     ▼
   User1 User2 Order1 Order2 Pay1 Pay2
        │          │           │
        └──────────┼───────────┘
                   ▼
          Redis Cache (Optional)
                   │
          Cache Hit? ── Yes → Return Response
                   │
                  No
                   ▼
               Database
                   │
                   ▼
          HTTP Response
                   │
                   ▼
                Client
```

---

## Responsibilities

### Reverse Proxy

- First entry point to the application.
- Handles HTTPS (SSL/TLS).
- Hides backend infrastructure.
- Filters malicious traffic.
- Can cache static content.
- May also perform load balancing.

---

### API Gateway

- Entry point for all APIs.
- Determines which microservice should handle the request.
- Authentication & Authorization.
- Rate limiting.
- API versioning.
- Request routing.

Example:

```
/users/*   → User Service
/orders/*  → Order Service
/payments/* → Payment Service
```

---

### Load Balancer

Each service may have multiple instances.

The Load Balancer chooses a healthy instance.

Example:

```
Order Service

Order1
Order2
Order3

↓

Load Balancer selects one instance.
```

---

### Microservices

Each service has one responsibility.

Examples:

- User Service
- Order Service
- Payment Service
- Product Service
- Notification Service

---

### Redis Cache

Checks whether the requested data is already cached.

- Cache Hit → Return immediately.
- Cache Miss → Query the Database.

---

### Database

Stores the permanent application data.

Examples:

- MySQL
- PostgreSQL
- MongoDB

---

## Request Flow

1. Client sends a request.
2. Reverse Proxy receives it.
3. API Gateway routes it to the correct service.
4. Service Load Balancer selects a service instance.
5. Service checks Redis.
6. If cache misses, query the Database.
7. Response is returned to the Client.

---

## One-Line Summary

Reverse Proxy
→ Protects the application.

API Gateway
→ Chooses the correct microservice.

Load Balancer
→ Chooses the correct instance of that microservice.

Microservice
→ Executes business logic.

Redis
→ Fast temporary storage.

Database
→ Permanent storage.


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
