# CN04 - Application Layer

# Navigation

- [1. Why Do We Need the Application Layer?](#1-why-do-we-need-the-application-layer)
- [2. What is the Application Layer?](#2-what-is-the-application-layer)
- [3. Client-Server Model](#3-client-server-model)
- [4. URL](#4-url)
- [5. DNS](#5-dns)
- [6. HTTP](#6-http)
- [7. HTTPS](#7-https)
- [8. Cookies](#8-cookies)
- [9. Sessions](#9-sessions)
- [10. JWT (Basics)](#10-jwt-basics)
- [11. REST APIs](#11-rest-apis)
- [12. Caching](#12-caching)
- [13. SMTP](#13-smtp)
- [14. FTP](#14-ftp)
- [15. SSH](#15-ssh)
- [16. HTTP Request](#16-http-request)
- [17. HTTP Response](#17-http-response)
- [18. HTTP Headers](#18-http-headers)
- [19. Statelessness](#19-statelessness)
- [20. Authentication vs Authorization](#20-authentication-vs-authorization)
- [21. HTTP vs HTTPS](#21-http-vs-https)
- [22. Common Interview Scenario](#22-common-interview-scenario)
- [23. Browser vs Server](#23-browser-vs-server)
- [24. REST API Example](#24-rest-api-example)
- [25. API Endpoint](#25-api-endpoint)
- [26. MIME Type](#26-mime-type)
- [27. Persistent Connections](#27-persistent-connections)
- [28. HTTP/1.1 vs HTTP/2 vs HTTP/3](#28-http11-vs-http2-vs-http3)
- [29. HTTP vs WebSocket](#29-http-vs-websocket)
- [30. Why is the Application Layer Important?](#30-why-is-the-application-layer-important)


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


# TLS Handshake (Simple Beginner Explanation)

## Before TLS

Imagine

you already know

the server's IP address.

```text
google.com

↓

142.xx.xx.xx
```

The browser

asks the OS

to connect.

```text
Browser

↓

OS

↓

TCP Connection

↓

Server OS
```

After the TCP 3-way handshake,

there is now

a reliable connection.

But...

**it is NOT secure.**

Anyone

between you

and the server

could read the data.

So,

before sending

the HTTP request,

the browser says,

> "Let's first make this connection secure."

That process

is called

**TLS Handshake.**

---

# What is the goal of the TLS Handshake?

The browser

and server

must solve

three problems.

```text
1.

Is this really Google?

↓

2.

How can only we
read the messages?

↓

3.

How do we create
a secret key
without anyone stealing it?
```

If these are solved,

communication becomes secure.

---

# What is Encryption?

Suppose

you want to send

```
Hello
```

Without encryption,

everyone sees

```
Hello
```

With encryption,

it becomes something like

```
8F92A71C...
```

Nobody can understand it.

Only someone

having the correct

**key**

can convert it back.

---

# What is a Key?

A key

is simply

a secret value

used for encryption

and decryption.

Think of it

like

a house key.

```text
Normal Message

↓

Encryption

+

Secret Key

↓

Encrypted Message

↓

Decryption

+

Same Secret Key

↓

Original Message
```

Without

the correct key,

the encrypted message

is useless.

---

# What is a Certificate?

Imagine

someone says

"I'm Google."

How do you know

they're telling the truth?

Anyone

could pretend

to be Google.

So,

Google has

a digital ID card.

That ID card

is called

a **Certificate.**

```text
Google

↓

Certificate

↓

"I really am Google."
```

---

# Who gives the Certificate?

Not Google itself.

A trusted organization

called a

**Certificate Authority (CA)**

issues it.

Think of it like

a government

issuing

a passport.

```text
Government

↓

Passport

↓

Person
```

Similarly,

```text
Certificate Authority

↓

Certificate

↓

Google
```

Examples

```text
DigiCert

Let's Encrypt

GlobalSign
```

Browsers

already trust

these Certificate Authorities.

---

# What is inside a Certificate?

A certificate

contains things like

```text
Website Name

↓

google.com

-------------------

Public Key

↓

Used during encryption

-------------------

Expiry Date

↓

Valid Until

-------------------

CA Signature

↓

Proof that
the certificate
is genuine
```

---

# What is a Public Key?

Remember

the browser

needs

a secret key

to encrypt data.

But

how can

the browser

send

that secret key

to Google

without anyone

stealing it?

It can't.

So

Google first sends

something called

a **Public Key.**

Think of it

like

an open lock.

Anyone

can lock it,

but

only Google

has the key

to open it.

```text
Public Key

↓

Lock

--------------------

Private Key

↓

Unlock
```

Google

keeps

the Private Key

secret forever.

---

# Why not just use the Public Key forever?

Because

public key encryption

is slow.

Very slow.

Instead,

it is used

only once

to safely create

a shared secret.

After that,

both sides

switch to

a much faster

secret key.

---

# What is a Shared Secret Key?

After the handshake,

both

Browser

and

Server

have

the exact same

secret key.

```text
Browser

↓

Secret Key

ABCDEFG

====================

Server

↓

Secret Key

ABCDEFG
```

Nobody else

knows this key.

Now

both sides

can encrypt

and decrypt

very quickly.

---

# Complete TLS Handshake

## Step 1

ClientHello

```text
Browser

↓

Server
```

The browser says

```text
Hello!

I want a secure connection.

Here are

the encryption methods

I support.
```

---

## Step 2

ServerHello

```text
Server

↓

Browser
```

The server replies

```text
Okay.

Let's use

this encryption method.
```

Now

both sides

agree

how

they will encrypt data.

---

## Step 3

Certificate

```text
Server

↓

Browser
```

The server sends

its certificate.

```text
Server

↓

Certificate

↓

Browser
```

The browser checks

```text
Is this certificate

valid?

↓

Is it signed

by a trusted

Certificate Authority?

↓

Is it expired?

↓

Does it belong

to google.com?
```

If any answer

is No,

the browser

shows

a security warning.

---

## Step 4

Create the Shared Secret

Now

the browser

creates

a random

secret key.

```text
Random Secret

↓

ABCD1234
```

The browser

locks

this secret

using

Google's

Public Key.

```text
Secret Key

↓

Encrypt using

Google's Public Key

↓

Send to Google
```

Even if

someone

captures this,

they cannot

open it.

Only Google

has

the Private Key

needed

to unlock it.

---

## Step 5

Server Unlocks It

Google

uses

its Private Key.

```text
Encrypted Secret

↓

Private Key

↓

Original Secret

↓

ABCD1234
```

Now

both

Browser

and

Google

know

the same

secret key.

---

# Secure Connection Ready

Now

the TLS Handshake

is finished.

```text
Browser

↓

Secret Key

ABCDEFG

==========================

Server

↓

Secret Key

ABCDEFG
```

From now on,

every HTTP request

is encrypted

using

this shared secret key.

---

# Now the HTTP Request Starts

```text
HTTP Request

↓

TLS

Encrypt

↓

TCP

↓

IP

↓

Internet

↓

IP

↓

TCP

↓

TLS

Decrypt

↓

HTTP

↓

Server
```

The routers

only see

encrypted bytes.

They never see

```http
GET /login

username=alice

password=123456
```

Only

the browser

and

the server

can read

the original message.

---

# Simple Mental Model

```text
TCP

↓

Builds a reliable road.

-------------------------

TLS

↓

Puts a locked tunnel
on that road.

-------------------------

HTTP

↓

Sends messages
through the locked tunnel.
```
