# CN04 - Application Layer

# Navigation


# 1. Why Do We Need the Application Layer?

## What is it?

The Application Layer

is the layer

that provides

network services

directly

to applications.

---

## Why?

The lower layers

only know

how to

move data.

They don't know

what

Chrome,

WhatsApp,

or Gmail

actually want.

Applications need

protocols

that define

how they communicate.

---

## How does it work?

```text
Chrome

↓

HTTP

↓

TCP

↓

IP

↓

Ethernet

↓

Internet
```

Each application

chooses

an appropriate

Application Layer protocol.

---

## Why is it designed this way?

Different applications

have

different needs.

```text
Web Browsing

↓

HTTP

--------------------

Email

↓

SMTP

--------------------

Remote Login

↓

SSH
```

Instead of

one protocol

doing everything,

each service

has

its own protocol.

---

# 2. What is the Application Layer?

## What is it?

The Application Layer

contains

protocols

used by

applications

to communicate

over a network.

---

## Why?

Without it,

applications

would have

no standard way

to exchange data.

---

## How does it work?

Example

```text
Browser

↓

HTTP

↓

Web Server

--------------------

Mail App

↓

SMTP

↓

Mail Server
```

---

## Why is it designed this way?

Applications

care about

messages,

not packets.

The lower layers

hide

all networking details.

---

# 3. Client-Server Model

## What is it?

Most applications

follow

the

Client-Server model.

```text
Client

↓

Requests

↓

Server

↓

Responds
```

---

## Why?

Keeping

data

on central servers

allows

many clients

to access it.

---

## How does it work?

```text
Browser

↓

HTTP Request

↓

Google Server

↓

HTTP Response

↓

Browser
```

---

## Why is it designed this way?

Servers

can serve

millions

of clients,

while clients

remain lightweight.

---

# 4. URL

## What is it?

A URL

(Uniform Resource Locator)

is

the address

of a resource

on the Internet.

---

## Example

```text
https://www.example.com:443/blog?id=10
```

---

## Parts

```text
https

↓

Protocol

--------------------

www.example.com

↓

Domain Name

--------------------

443

↓

Port

--------------------

/blog

↓

Path

--------------------

id=10

↓

Query Parameters
```

---

## Why?

Humans

remember

names

better

than IP addresses.

---

## How does it work?

```text
Enter URL

↓

DNS finds IP

↓

Browser connects

↓

HTTP Request
```

---

## Why is it designed this way?

One URL

contains

everything needed

to locate

a resource.

---

# 5. DNS

## What is DNS?

DNS

(Domain Name System)

translates

Domain Names

into

IP Addresses.

---

## Why?

Computers

understand

IP Addresses.

Humans

remember

names.

---

## How does it work?

```text
google.com

↓

DNS

↓

142.250.x.x

↓

Connect
```

---

## Why is it designed this way?

Changing

a server's IP

shouldn't require

users

to remember

new numbers.

---

# 6. HTTP

## What is HTTP?

HTTP

(HyperText Transfer Protocol)

is

the protocol

used

to transfer

web pages

and APIs.

---

## Why?

Browsers

and servers

need

a common language.

---

## How does it work?

```text
Browser

↓

HTTP Request

↓

Server

↓

HTTP Response

↓

Browser
```

---

## Request

Contains

```text
Method

Headers

Body
```

---

## Response

Contains

```text
Status Code

Headers

Body
```

---

## Common Methods

```text
GET

↓

Read

--------------------

POST

↓

Create

--------------------

PUT

↓

Replace

--------------------

PATCH

↓

Update

--------------------

DELETE

↓

Delete
```

---

## Common Status Codes

```text
200

↓

OK

--------------------

201

↓

Created

--------------------

301

↓

Moved Permanently

--------------------

400

↓

Bad Request

--------------------

401

↓

Unauthorized

--------------------

403

↓

Forbidden

--------------------

404

↓

Not Found

--------------------

500

↓

Internal Server Error
```

---

## Why is HTTP designed this way?

Requests

and Responses

keep communication

simple

and independent.

---

# 7. HTTPS

## What is HTTPS?

HTTPS

is

HTTP

running over

TLS.

---

## Why?

HTTP

sends data

as plain text.

Anyone

can read it.

---

## How does it work?

```text
Browser

↓

TLS

↓

Encrypted Connection

↓

Server

↓

HTTP Messages
```

---

## Why is it designed this way?

Encryption

provides

```text
Privacy

Integrity

Authentication
```

---

# 8. Cookies

## What are Cookies?

Cookies

are

small pieces

of data

stored

by

the browser.

---

## Why?

HTTP

is stateless.

Servers

forget

every request.

Cookies

help

remember users.

---

## How does it work?

```text
Login

↓

Server

↓

Set-Cookie

↓

Browser Stores

↓

Future Requests

↓

Cookie Sent Back
```

---

## Why is it designed this way?

The browser

automatically

sends cookies

with matching requests,

making

user identification

easy.

---

# 9. Sessions

## What are Sessions?

A Session

stores

user information

on

the server.

---

## Why?

Sensitive data

should not

be stored

inside

the browser.

---

## How does it work?

```text
Login

↓

Server

↓

Create Session

↓

Store Session ID

↓

Cookie

↓

Future Requests

↓

Lookup Session
```

---

## Why is it designed this way?

Only

a small

Session ID

travels

over the network.

The actual data

remains

on the server.

---

# 10. JWT (Basics)

## What is JWT?

JWT

(JSON Web Token)

is

a signed token

used

for authentication.

---

## Why?

Servers

may not want

to store

sessions.

JWT

stores

user information

inside

the token.

---

## How does it work?

```text
Login

↓

Server

↓

Generate JWT

↓

Client Stores

↓

Future Requests

↓

Authorization Header

↓

Server Verifies
```

---

## Why is it designed this way?

The server

only verifies

the token.

It doesn't

need

to store

session data.

---

# 11. REST APIs

## What is REST?

REST

is

an architectural style

for designing

web APIs.

---

## Why?

Applications

need

a standard way

to communicate.

---

## How does it work?

Example

```text
GET

/users

↓

Fetch Users

--------------------

POST

/users

↓

Create User

--------------------

DELETE

/users/5

↓

Delete User
```

---

## Why is it designed this way?

Resources

are represented

using URLs,

while

HTTP methods

describe

the action.

---

# 12. Caching

## What is Caching?

Caching

means

storing

frequently used

data

for

faster access.

---

## Why?

Fetching data

every time

is slow.

---

## How does it work?

```text
Request

↓

Cache?

↓

Yes

↓

Return Cached Data

--------------------

No

↓

Fetch

↓

Store

↓

Return
```

---

## Why is it designed this way?

Trading

a little memory

for

much faster

response time.

---

# 13. SMTP

## What is SMTP?

SMTP

(Simple Mail Transfer Protocol)

is used

to send

emails.

---

## Why?

Mail servers

need

a standard protocol

for email delivery.

---

## How does it work?

```text
Mail Client

↓

SMTP Server

↓

Recipient Mail Server
```

---

## Why is it designed this way?

Email

may pass

through

multiple servers

before reaching

the destination.

---

# 14. FTP

## What is FTP?

FTP

(File Transfer Protocol)

is used

to transfer

files

between

computers.

---

## Why?

Large files

need

a dedicated

transfer protocol.

---

## How does it work?

```text
FTP Client

↓

FTP Server

↓

Upload / Download
```

---

## Why is it designed this way?

Optimized

for

file transfer,

not

web browsing.

---

# 15. SSH

## What is SSH?

SSH

(Secure Shell)

is

a protocol

used

to securely

access

another computer.

---

## Why?

Administrators

need

remote access

without

exposing

passwords

or commands.

---

## How does it work?

```text
SSH Client

↓

Encrypted Connection

↓

Remote Machine
```

---

## Why is it designed this way?

Commands,

passwords,

and responses

are encrypted,

making

remote administration

secure.

# 16. HTTP Request

## What is it?

An HTTP Request

is a message

sent

by a client

to request

a resource

from a server.

---

## Why?

The server

cannot know

what the client wants

unless

the client asks.

---

## How does it work?

```text
Browser

↓

HTTP Request

↓

Server

↓

HTTP Response
```

Example

```http
GET /users HTTP/1.1
Host: example.com
```

---

## Main Parts

```text
Request Line

↓

Headers

↓

Body (Optional)
```

---

## Why is it designed this way?

Different requests

need

different information.

Headers

carry metadata,

while

the body

carries actual data.

---

# 17. HTTP Response

## What is it?

An HTTP Response

is

the server's reply

to an HTTP Request.

---

## Why?

The client

needs

the requested data

or

an error message.

---

## How does it work?

Example

```http
HTTP/1.1 200 OK

Content-Type: application/json

{
    "name":"John"
}
```

---

## Main Parts

```text
Status Line

↓

Headers

↓

Body
```

---

## Why is it designed this way?

Separating

status,

metadata,

and data

makes

responses

easy to process.

---

# 18. HTTP Headers

## What are Headers?

Headers

are

extra information

about

the request

or response.

---

## Why?

Not everything

belongs

inside

the body.

Some information

describes

the communication itself.

---

## Common Headers

```text
Host

↓

Target Server

--------------------

Authorization

↓

Authentication

--------------------

Content-Type

↓

Data Format

--------------------

Cookie

↓

User Information

--------------------

Accept

↓

Expected Response Format
```

---

## Why is it designed this way?

Keeps

metadata

separate

from

actual content.

---

# 19. Statelessness

## What is it?

HTTP

is

Stateless.

Each request

is

independent.

---

## Why?

Keeping

server memory

for every client

would consume

huge resources.

---

## How does it work?

```text
Request 1

↓

Processed

↓

Forgotten

---------------------

Request 2

↓

Processed

↓

Forgotten
```

The server

doesn't remember

previous requests.

---

## Why is it designed this way?

Stateless systems

are

simpler,

faster,

and easier

to scale.

Cookies,

Sessions,

and JWT

are used

when memory

is needed.

---

# 20. Authentication vs Authorization

## Authentication

### What is it?

Verifies

who you are.

---

### Example

```text
Username

+

Password
```

---

### Why?

The server

must identify

the user.

---

## Authorization

### What is it?

Determines

what

you are allowed

to access.

---

### Example

```text
Admin

↓

Delete User

--------------------

Normal User

↓

Cannot Delete User
```

---

## Mental Model

```text
Authentication

↓

Who are you?

--------------------

Authorization

↓

What can you do?
```

---

# 21. HTTP vs HTTPS

| HTTP | HTTPS |
|------|-------|
|No Encryption|Encrypted|
|Less Secure|More Secure|
|Port 80|Port 443|
|Uses HTTP|Uses HTTP + TLS|

---

## Why HTTPS?

Without encryption,

anyone

between

client

and server

can read

the data.

HTTPS

prevents

this.

---

# 22. Common Interview Scenario

Suppose

you type

```text
https://google.com
```

What happens?

```text
Enter URL

↓

DNS

Find IP Address

↓

TCP Connection

↓

TLS Handshake

↓

HTTP Request

↓

Server Processes

↓

HTTP Response

↓

Browser Renders Page
```

This

is one of

the most common

SDE interview questions.

---

# 23. Browser vs Server

| Browser | Server |
|----------|--------|
|Sends Request|Processes Request|
|Displays UI|Stores Data|
|Runs on User Device|Runs in Data Center|

---

## Why?

Separating

client

and server

allows

millions

of users

to access

the same application.

---

# 24. REST API Example

Suppose

you're building

a Todo App.

```text
GET

/todos

↓

Fetch Todos

----------------------

POST

/todos

↓

Create Todo

----------------------

PUT

/todos/5

↓

Replace Todo

----------------------

PATCH

/todos/5

↓

Update Todo

----------------------

DELETE

/todos/5

↓

Delete Todo
```

---

# 25. API Endpoint

## What is it?

An Endpoint

is

a specific URL

that provides

a service.

---

## Example

```text
/users

↓

All Users

---------------------

/users/5

↓

User 5

---------------------

/products

↓

Products
```

---

## Why?

Different resources

need

different URLs.

---

## How does it work?

```text
Request

↓

Endpoint

↓

Server Function

↓

Response
```

---

# 26. MIME Type

## What is it?

A MIME Type

tells

the receiver

what type

of data

is being sent.

---

## Examples

```text
text/html

↓

HTML Page

-------------------

application/json

↓

JSON

-------------------

image/png

↓

PNG Image

-------------------

application/pdf

↓

PDF File
```

---

## Why?

The browser

must know

how

to interpret

the received data.

---

# 27. Persistent Connections

## What is it?

HTTP/1.1

can reuse

the same

TCP connection

for multiple requests.

---

## Why?

Creating

a new TCP connection

for every request

is expensive.

---

## How does it work?

```text
TCP Connection

↓

Request

↓

Response

↓

Request

↓

Response

↓

Close
```

---

## Why is it designed this way?

Reusing

connections

reduces

latency

and improves

performance.

---

# 28. HTTP/1.1 vs HTTP/2 vs HTTP/3

| HTTP/1.1 | HTTP/2 | HTTP/3 |
|-----------|---------|---------|
|Text|Binary|Binary|
|One Request at a Time*|Multiplexing|Multiplexing|
|TCP|TCP|QUIC (UDP)|

\*Pipelining existed but wasn't widely effective.

---

## Why were newer versions created?

To reduce

latency,

improve

speed,

and support

many simultaneous requests.

---

# 29. HTTP vs WebSocket

## HTTP

```text
Client

↓

Request

↓

Server

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

Both sides

can send

messages

any time.
```

---

## Why?

Applications

like

```text
Chat

Gaming

Live Notifications
```

need

real-time communication.

---

# 30. Why is the Application Layer Important?

Every application

uses

Application Layer protocols.

```text
Web Browsing

↓

HTTP

--------------------

Secure Browsing

↓

HTTPS

--------------------

Email

↓

SMTP

--------------------

Remote Login

↓

SSH

--------------------

File Transfer

↓

FTP
```

Without

the Application Layer,

applications

would have

no standard way

to communicate.
