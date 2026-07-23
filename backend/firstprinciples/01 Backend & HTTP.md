# 01 Backend & HTTP

# 1. What is a Backend?

## Definition

A Backend is the part of an application that:

- Receives requests from clients.
- Executes business logic.
- Interacts with databases or other services.
- Sends a response back.

The backend runs on servers, not on the user's device.

---

## Simple Flow

Client

↓

Frontend (Browser/App)

↓

Backend (Server)

↓

Database / Other Services

↓

Backend

↓

Frontend

↓

User

---

## Responsibilities

- Authentication
- Authorization
- Business Logic
- Database Operations
- File Storage
- API Creation
- Sending Responses

---

## Example

User clicks:

"Login"

↓

Frontend sends username & password.

↓

Backend verifies credentials.

↓

Database checks user.

↓

Backend generates JWT.

↓

Frontend receives token.

↓

User logged in.

---

## Key Point

Frontend = User Interface

Backend = Brain of the application
# 2. How Backends Work

## Request Flow

Client

↓

HTTP Request

↓

Server

↓

Backend Application

↓

Business Logic

↓

Database / External APIs

↓

Backend

↓

HTTP Response

↓

Client

---

## Steps

1. Client sends an HTTP request.

2. Server receives it.

3. Backend decides what to do.

4. Reads/Writes data.

5. Creates response.

6. Sends response.

---

## Example

GET /users/10

↓

Backend receives request.

↓

Queries database.

↓

Gets user data.

↓

Returns JSON.

```json
{
  "id": 10,
  "name": "John"
}
```

---

## Backend Executes

- Java
- Node.js
- Python
- Go
- C#
- etc.

The client never executes backend code.

# 3. Why Do We Need a Backend?

Without a backend:

- No database access.
- No authentication.
- No business logic.
- No secure secrets.
- No payment processing.
- No centralized data.

---

## Backend Provides

- Security
- Data Storage
- Validation
- Authentication
- Authorization
- Scalability
- APIs

---

## Example

Bank Transfer

User

↓

Frontend sends request.

↓

Backend verifies:

- User logged in?
- Balance available?
- Receiver exists?

↓

Updates database.

↓

Returns success.

---

## Key Point

Anything requiring trust or security belongs in the backend.


# 4. How Frontends Work

## Responsibilities

Frontend is responsible for:

- Displaying UI
- Taking user input
- Calling backend APIs
- Showing responses

---

## Flow

Browser

↓

Downloads HTML

↓

Downloads CSS

↓

Downloads JavaScript

↓

Browser executes JavaScript

↓

JavaScript calls Backend APIs

↓

Displays response

---

## Technologies

- HTML
- CSS
- JavaScript
- React
- Angular
- Vue

---

## Key Point

Frontend runs inside the user's browser.

# 5. Why Can't Backend Logic Be Written in the Frontend?

Because frontend code is public.

Anyone can:

- View it.
- Modify it.
- Copy it.
- Bypass it.

---

## Never Store

- Database Passwords
- API Keys
- JWT Secret
- Payment Secrets
- Business Rules

in frontend code.

---

## Example

Bad

```js
if (balance >= amount)
    transferMoney();
```

User can modify it.

Good

Frontend

↓

Transfer Request

↓

Backend checks balance.

↓

Database updates.

↓

Returns success.

---

## Rule

Frontend = Untrusted

Backend = Trusted

# 6. Frontend vs Backend Execution

## Frontend

Server

↓

Sends HTML, CSS, JavaScript

↓

Browser executes JavaScript

↓

User interacts with UI

---

## Backend

Client sends Request

↓

Backend executes code

↓

Reads/Writes Database

↓

Returns Response

---

## Simple Example

Frontend

```js
fetch("/api/profile");
```

Browser sends request.

---

Backend

```js
app.get("/api/profile", ...)
```

Runs on server.

Queries database.

Returns JSON.

---

Frontend

Receives JSON.

Updates UI.

---

## Key Difference

Frontend code executes on the user's device.

Backend code executes on the server.



# HTTP vs WebSocket

## HTTP
- Request → Response → End
- Stateless
- Best for CRUD APIs (login, register, payments, uploads)
- Easy to scale
- Sends HTTP headers with every request
- Real-time requires **polling**

## WebSocket
- Persistent connection
- Full-duplex (client & server can send anytime)
- Best for real-time apps (chat, games, notifications)
- HTTP headers only during initial handshake
- No polling, server pushes updates instantly
- Keeps connections open → more memory & harder to scale

---

## Tradeoff

| HTTP | WebSocket |
|------|-----------|
| Request/Response | Persistent Connection |
| Stateless | Stateful (connection) |
| Easy to scale | Harder to scale |
| Header on every request | Header only once |
| Polling for real-time | Instant server push |
| CRUD APIs | Real-time communication |

---

## Use

**HTTP:** Login, Register, REST APIs, CRUD, Payments, Uploads

**WebSocket:** Chat, Notifications, Live Dashboards, Games, Collaborative Apps

---

## Golden Line

**HTTP is optimized for simple request-response communication, while WebSocket is optimized for persistent, bidirectional, real-time communication.**





* 5. Understanding HTTP for backend engineers, where it all starts *

### 1. Core Principles of HTTP
HTTP (Hypertext Transfer Protocol) is an application-layer protocol (Layer 7 in the OSI model) used by clients and servers to communicate. It is built on two fundamental ideas:
*   Statelessness: The server retains no memory of past interactions. Every request is entirely self-contained and must include all the necessary information (like authentication tokens or cookies) for the server to process it. 
    *   Benefits: This simplifies server architecture and improves scalability, because a single server doesn't need to keep track of user sessions, and a server crash won't destroy a client's state.
*   Client-Server Model: Communication is always initiated by the client (e.g., a web browser) to request resources or actions, and the server waits for these requests to process and respond.

### 2. Transport Protocol & HTTP Versions
HTTP relies on a reliable, connection-based transport protocol, almost universally *TCP (Transmission Control Protocol)*. Over the years, HTTP has evolved to improve how these TCP connections are handled:
*   HTTP 1.0: Opened a new TCP connection for every single request and response, which was highly inefficient and slow.
*   HTTP 1.1: Introduced persistent connections (keep-alive) as the default, allowing multiple requests to be sent over a single reused connection.
*   HTTP 2.0: Introduced multiplexing (multiple requests/responses concurrently on one connection), binary framing, header compression, and server push.
*   HTTP 3.0: Replaced TCP with QUIC (built over UDP) to establish faster connections and handle packet loss better, eliminating head-of-line blocking.

### 3. Anatomy of HTTP Messages
Client-server communication happens via structured text messages.
*   Request Message (Client to Server): Contains a Request Method (e.g., GET/POST), the Resource URL, the HTTP version, Host domain, Headers, a blank line, and an optional Request Body.
*   Response Message (Server to Client): Contains the HTTP version, a Status Code (e.g., 200), a Status Value (e.g., OK), Headers, a blank line, and the Response Body.

### 4. HTTP Headers
Headers are key-value pairs that act as metadata for the package being transmitted, allowing the system to be highly extensible and act as a "remote control" to dictate server behavior. 
*   Request Headers: Sent by the client to provide context (e.g., User-Agent identifies the browser, Authorization sends credentials).
*   General Headers: Apply to both requests and responses (e.g., Date, Connection, Cache-Control).
*   Representation Headers: Describe the message body (e.g., Content-Type for media format like JSON/HTML, Content-Length for byte size, Content-Encoding for gzip compression).
*   Security Headers: Protect against attacks (e.g., Strict-Transport-Security forces HTTPS, Content-Security-Policy prevents cross-site scripting, Set-Cookie with HTTP-only flags).

### 5. HTTP Methods and Idempotency
Methods define the semantic intent of the client's request.
*   GET: Fetches data from the server without modifying anything.
*   POST: Submits new data to the server (includes a request body).
*   PATCH: Partially updates an existing resource.
*   PUT: Completely replaces an existing resource with the provided body.
*   DELETE: Removes a resource.
*   OPTIONS: Inquires about the server's capabilities (used heavily in CORS).

Idempotency is a crucial concept here:
*   Idempotent Methods: Can be executed multiple times and yield the exact same result on the server state (e.g., GET, PUT, DELETE).
*   Non-Idempotent Methods: Running them multiple times creates different results (e.g., submitting a POST request twice creates two separate resources).

### 6. Cross-Origin Resource Sharing (CORS)
Browsers enforce a Same-Origin Policy, blocking web apps from making requests to different domains (origins). CORS is a security mechanism to bypass this safely.
*   Simple Requests: (Usually GET or POST with standard headers/content types). The browser automatically adds an Origin header. If the server allows the request, it replies with the Access-Control-Allow-Origin header containing the client's domain (or a * wildcard). If missing, the browser blocks the response.
*   Pre-flight Requests: Triggered if a request uses a non-simple method (PUT/DELETE), requires authorization headers, or uses a application/json content type. 
    *   The browser first fires an OPTIONS request asking the server if the route supports the intended method and headers.
    *   The server replies with a 204 No Content status, explicitly listing allowed origins, methods, headers, and a max-age to cache this configuration. 
    *   If successful, the browser then sends the actual, original request.

### 7. Standardized Status Codes
Status codes are three-digit numbers that act as a universal language to indicate the outcome of a request.
*   1xx (Informational): Indicates headers received; client can proceed (e.g., 100 Continue for large uploads).
*   2xx (Success): 
    *   200 OK: Successful operation.
    *   201 Created: Usually follows a POST request.
    *   204 No Content: Successful, but no body to return (used in OPTIONS or DELETE).
*   3xx (Redirection):
    *   301 Moved Permanently: The resource has a new URL.
    *   302 Found/Temporary Redirect: Temporarily forward to a new route.
    *   304 Not Modified: Tells the client to use its locally cached version.
*   4xx (Client Errors): 
    *   400 Bad Request: Invalid data format sent by client.
    *   401 Unauthorized: Missing or invalid authentication token.
    *   403 Forbidden: Authenticated, but lacks necessary permissions.
    *   404 Not Found: Incorrect URL or deleted resource.
    *   405 Method Not Allowed: Using the wrong method for a route.
    *   409 Conflict: Business logic violation (e.g., duplicate username).
    *   429 Too Many Requests: Client has hit rate limits.
*   5xx (Server Errors):
    *   500 Internal Server Error: An unhandled exception crashed the server.
    *   501 Not Implemented: Feature not yet supported.
    *   502 Bad Gateway / 504 Gateway Timeout: Issues originating from proxies or load balancers failing to reach upstream servers.
    *   503 Service Unavailable: Server down or under maintenance.

### 8. HTTP Caching
Caching reuses previously downloaded responses to save bandwidth and load times.
*   When a client first fetches a resource, the server responds with the payload alongside three headers: Cache-Control (sets max duration), ETag (a unique hash of the payload), and Last-Modified.
*   On subsequent requests, the client sends conditional headers: If-None-Match (carrying the ETag) or If-Modified-Since.
*   If the data on the server hasn't changed, the server saves bandwidth by sending an empty 304 Not Modified response, instructing the browser to use its cached copy. If it has changed, it sends a 200 OK with the new data and a new ETag.

### 9. Content Negotiation and Compression
Clients and servers can negotiate the best format to exchange data.
*   The client sends preferences via Accept (e.g., application/json vs application/xml), Accept-Language (e.g., en vs es), and Accept-Encoding (e.g., gzip).
*   The server responds with the appropriate format.
*   Compression: By negotiating an encoding like gzip, a server can drastically compress text responses (e.g., shrinking a 26MB JSON payload down to 3.8MB) to save massive amounts of network bandwidth.

### 10. Handling Large Data Transfers
*   Large Client Uploads (Images/Video): Standard JSON is terrible for binary data. Instead, clients use a multipart/form-data request. This breaks the file into chunks separated by a unique string delimiter defined in the boundary header.
*   Large Server Downloads: To prevent timing out, the server streams the file in chunks using Content-Type: text/event-stream and Connection: keep-alive. The browser continually appends these chunks until the transfer finishes.

### 11. Security (SSL/TLS & HTTPS)
*   TLS (Transport Layer Security): The modern, secure replacement for the outdated SSL protocol.
*   It encrypts data in transit to prevent interception (eavesdropping) or tampering, utilizing certificates to verify the server's identity.
*   HTTPS: Simply the standard HTTP protocol wrapped inside a secure TLS connection

---

# 7. Backend Mental Model

## What actually happens?

User clicks a button.

↓

Frontend (JavaScript)

↓

HTTP Request

↓

Internet

↓

Backend Server

↓

Business Logic

↓

Database / Cache / External APIs

↓

Backend creates Response

↓

HTTP Response

↓

Frontend receives data

↓

Browser updates UI

---

## Frontend vs Backend

Frontend

- Runs on user's device.
- Shows UI.
- Takes user input.
- Calls backend APIs.
- Cannot directly access database.

Backend

- Runs on server.
- Executes business logic.
- Accesses databases.
- Authenticates users.
- Returns data.

---

## What does the Browser execute?

The browser executes only:

- HTML
- CSS
- JavaScript

The browser NEVER executes:

- Node.js
- Java
- Python
- Go
- C#
- SQL queries

Those execute only on the server.

---

## Why isn't backend code sent to the browser?

Imagine an online banking app.

If backend code was sent to every user,

they could:

- Read your database password.
- Remove payment validation.
- Generate fake admin accounts.
- Bypass authentication.

Therefore,

only the RESULT is sent,

never the backend code.

---

## What travels over the Internet?

Frontend → Backend

- HTTP Request

Backend → Frontend

- HTTP Response

Not source code.

Usually JSON data.

Example:

Request

```http
GET /api/profile
```

Response

```json
{
  "name": "John",
  "age": 22
}
```

---

## Remember

Frontend receives code.

Browser executes it.

↓

Frontend sends HTTP requests.

↓

Backend receives requests.

↓

Backend executes server-side code.

↓

Backend returns data.

↓

Frontend displays the data.

---

## One-Line Interview Answer

Frontend = Presentation Layer

Backend = Processing Layer

Database = Storage Layer

HTTP = Communication Layer

JSON = Data Exchange Format
