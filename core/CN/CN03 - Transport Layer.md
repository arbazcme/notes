# CN03 - Transport Layer (TCP & UDP)

# Navigation

- [1. Why Do We Need the Transport Layer?](#1-why-do-we-need-the-transport-layer)
- [2. What is the Transport Layer?](#2-what-is-the-transport-layer)
- [3. Process-to-Process Communication](#3-process-to-process-communication)
- [4. Ports](#4-ports)
- [5. Socket](#5-socket)
- [6. Multiplexing & Demultiplexing](#6-multiplexing--demultiplexing)
- [7. TCP vs UDP (Overview)](#7-tcp-vs-udp-overview)

---

# 1. Why Do We Need the Transport Layer?

So far,

we know

how a Packet

travels

from

one device

to another.

Question:

Suppose

your laptop

receives

a Packet.

Who should receive it?

```text
Chrome?

↓

Spotify?

↓

WhatsApp?

↓

VS Code?

↓

Discord?
```

The

Network Layer

only knows

the

Destination IP.

It does **not**

know

which application

should receive

the data.

We need

another layer.

That is

```text
Transport Layer.
```

---


# NAT vs Transport Layer | Ports & Sockets

## NAT (Router)
- Exists **inside the router**.
- Uses a **NAT table**.
- Maps:
  ```
  Public IP:Port ↔ Private IP:Port
  ```
- Purpose: Forward packets to the correct **device**.
- Does **not** know which application receives the packet.

---

## Transport Layer (Operating System)
- Exists **inside the OS**.
- Implements TCP/UDP.
- Purpose:
  - Create/manage sockets.
  - Port → Socket lookup.
  - Deliver packets to the correct application.
  - (TCP) Reliability, retransmission, flow control, congestion control.

---

## Packet Flow

```
Internet
    │
    ▼
Router (NAT)
    │
    ▼
Laptop
    │
    ▼
IP Layer
(Is destination IP mine?)
    │
    ▼
Transport Layer
(Read destination port)
    │
    ▼
Port → Socket Table
    │
    ▼
Socket
    │
    ▼
Application
```

---

## Port
- Just a **16-bit number**.
- Identifies a communication endpoint.
- Used by the OS to find the correct socket.

Examples:
- 80 → HTTP
- 443 → HTTPS
- 52345 → Ephemeral port

---

## Socket
An **OS-managed communication endpoint**.

Contains:
- Local IP
- Local Port
- Remote IP
- Remote Port
- Protocol (TCP/UDP)
- Connection State
- Buffers
- Reference to owning process

Applications communicate through **sockets**, not ports.

---

## Relationship

```
Port
 │
 ▼
Socket Table
 │
 ▼
Socket
 │
 ▼
Application
```

---

## Interview Definitions

**Port:** Numeric identifier used by the OS to locate sockets.

**Socket:** OS-managed communication endpoint containing connection information and linked to an application.



# 2. What is the Transport Layer?

## What is it?

The Transport Layer

is responsible for

**process-to-process communication.**

It delivers

the received data

to the

correct application

running

on a device.

Think

of

the layers.

```text
Network Layer

↓

Correct Device

-------------------

Transport Layer

↓

Correct Application
```

---

## Why is it needed?

Imagine

two laptops.

```text
Laptop A

↓

Laptop B
```

Laptop B

is running

```text
Chrome

WhatsApp

Spotify

VS Code
```

A Packet

reaches

Laptop B.

Question

Which application

should receive it?

Without

the Transport Layer,

the OS

cannot decide.

---

## How does it work?

The Transport Layer

uses

```text
Port Numbers.
```

Each application

communicates

using

a Port.

Example

```text
Chrome

↓

Port 50000

--------------

Spotify

↓

Port 51000

--------------

Node Server

↓

Port 3000
```

When data arrives,

the OS checks

the

Destination Port

and

delivers

the data

to

that application.

---

## Why is it designed this way?

Imagine

an apartment.

```text
Building

↓

IP Address

Flat Number

↓

Port Number
```

The courier

first reaches

the correct building.

Then

the flat number

decides

which person

receives

the package.

Exactly

the same happens

in networking.

```text
IP

↓

Correct Device

---------------

Port

↓

Correct Application
```

---

# 3. Process-to-Process Communication

## What is it?

A

Process

is simply

a running program.

Examples

```text
Chrome

↓

Process

----------------

WhatsApp

↓

Process

----------------

Node Server

↓

Process
```

The Transport Layer

communicates

between

processes,

not just

between devices.

---

## Why?

Suppose

Chrome

requests

Google.

At the same time,

Spotify

downloads

a song.

Both packets

reach

your laptop.

Question

How does

the OS know

which Packet

belongs to

Chrome

and which

belongs to

Spotify?

Answer

```text
Port Numbers.
```

---

## Mental Model

```text
Network Layer

↓

Device

--------------

Transport Layer

↓

Application
```

---

# 4. Ports

## What is a Port?

A Port

is a

logical number

used to identify

an application

inside

a device.

A Port

is

NOT

a physical socket

on your laptop.

It exists

only

inside

the Operating System.

---

## Why do we need Ports?

Suppose

Google

sends data

to your laptop.

Destination IP

identifies

your laptop.

Question

How does

Google know

whether

Chrome

or

Spotify

requested it?

It doesn't.

Instead,

Google

copies

the

Destination Port

from

your request.

The OS

uses

that Port

to deliver

the data

to

the correct process.

---

## Port Range

```text
0 - 65535
```

Some ports

are

well known.

```text
80

↓

HTTP

------------

443

↓

HTTPS

------------

22

↓

SSH

------------

25

↓

SMTP
```

Most client

applications

use

temporary

(Ephemeral)

ports,

such as

```text
52341

55120

60015
```

chosen

automatically

by the OS.

---

## Why is it designed this way?

Using

numbers

is

simple,

fast,

and

allows

thousands

of applications

to communicate

simultaneously

on one device.

---

# 5. Socket

## What is a Socket?

A Socket

is an endpoint

used

by an application

to send

or receive

data.

Think

of

it as

the communication channel

between

an application

and

the Operating System.

---

## Why do we need Sockets?

Applications

should not

directly

access

the Network Card.

Instead,

they ask

the OS.

Example

```text
Chrome

↓

Socket

↓

Operating System

↓

Network
```

The OS

handles

TCP,

IP,

Ethernet,

and

the hardware.

Applications

simply

read

and

write

using

Sockets.

---

## Socket = ?

A Socket

is uniquely identified by

```text
IP Address

+

Port Number
```

Example

```text
192.168.1.10

:

52341
```

This identifies

one communication endpoint.

---



# 6. Multiplexing & Demultiplexing

## What is it?

Suppose

three applications

are sending data.

```text
Chrome

Spotify

WhatsApp
```

The Transport Layer

collects

all outgoing data

and sends it

through

the same

network interface.

This is called

```text
Multiplexing.
```

---

When data

returns,

the Transport Layer

separates

the incoming data

and delivers it

to

the correct application.

This is called

```text
Demultiplexing.
```

---

## Why?

Without this,

only

one application

could use

the network

at a time.

---

## Mental Model

```text
Outgoing

Many Apps

↓

One Network

(Multiplexing)

-------------------

Incoming

One Network

↓

Many Apps

(Demultiplexing)
```

---


# NIC (Network Interface) & Multiplexing

## Network Interface (NIC)
A **Network Interface Card (NIC)** is the hardware (or virtual hardware) that sends and receives packets.

Examples:
- Ethernet
- Wi-Fi
- Virtual NIC

Think of it as the computer's **door to the network**.

---

## Outgoing Data

```
Applications
     │
Sockets
     │
Transport Layer
     │
IP Layer
     │
NIC
     │
Router
```

Applications write data to sockets.

The Transport Layer converts the data into packets.

The NIC transmits those packets.

---

## Multiplexing

Many applications share **one NIC**.

It **does not** combine data into one giant packet.

Instead, packets are **interleaved**.

Example:

```
Chrome Packet
↓

Spotify Packet
↓

Chrome Packet
↓

WhatsApp Packet
↓

VS Code Packet
```

The order depends on:
- Data availability
- Socket buffers
- TCP state
- Driver queues
- OS scheduling

It is **not** strict Round Robin.

---

## Demultiplexing

Incoming packet:

```
NIC
 │
 ▼
IP Layer
 │
 ▼
Transport Layer
 │
(Read destination port)
 │
 ▼
Socket
 │
 ▼
Application
```

---

## Mental Model

### CPU

```
Many Threads
     │
     ▼
One CPU
```

### Network

```
Many Applications
      │
      ▼
One NIC
```

Both share one resource.

- CPU → execution time
- NIC → transmission of packets

```text
Conceptually, multiplexing is like Round Robin: multiple applications share one communication resource
 so that all can make progress concurrently. However, the actual scheduling is not strict Round Robin;
it depends on packet availability, TCP state, buffers, priorities, and the network driver.

```


# 7. TCP vs UDP (Overview)

The Transport Layer

mainly provides

two protocols.

```text
TCP

UDP
```

Both

perform

process-to-process

communication,

but

they are designed

for

different goals.

| TCP | UDP |
|------|------|
|Reliable|Best Effort|
|Connection-Oriented|Connectionless|
|Ordered Delivery|No Ordering|
|Acknowledgements|No Acknowledgements|
|Slower|Faster|

---

## Why two protocols?

Question

Why not

always use

TCP?

Because

reliability

has a cost.

TCP

adds

```text
Connection Setup

↓

Acknowledgements

↓

Retransmissions

↓

Ordering
```

making it

slower.

Sometimes,

speed

is

more important.

Examples

```text
Video Calls

Gaming

Live Streaming
```

Missing

one packet

is acceptable,

but

waiting

for retransmission

is not.

UDP

is perfect

there.

---

## Quick Revision

```text
Network Layer

↓

Finds Device

(IP)

--------------------

Transport Layer

↓

Finds Application

(Port)

--------------------

Socket

↓

Application Endpoint

(IP + Port)

--------------------

TCP

↓

Reliable

--------------------

UDP

↓

Fast
```

---

## Interview Questions

### What is the Transport Layer?

```text
Provides

process-to-process

communication

using

Port Numbers.
```

---

### Difference between

IP Address

and

Port?

```text
IP

↓

Identifies Device

----------------

Port

↓

Identifies Application
```

---

### What is a Socket?

```text
A communication endpoint

identified by

IP Address

and

Port Number.
```

---

### Why do we need Ports?

```text
To deliver

incoming data

to

the correct

application.
```

---

# Bridge

Now,

we know

why

Ports

and

Sockets

exist.

The next question is

```text
How does

TCP

provide

reliable communication?

↓

Connection

↓

Three-Way Handshake

↓

Sequence Numbers

↓

Acknowledgements

↓

Retransmissions

↓

Connection Termination
```

These concepts

form

the core

of

TCP.

# 8. TCP (Transmission Control Protocol)

## What is TCP?

TCP

(Transmission Control Protocol)

is a

**connection-oriented**

Transport Layer protocol

that provides

```text
Reliable

Ordered

Error-checked

delivery of data
```

between

two applications.

---

## Why do we need TCP?

Imagine

you are downloading

a PDF.

```text
Server

↓

Internet

↓

Your Laptop
```

Suppose

Packet 5

gets lost.

Without TCP,

your file

becomes

corrupted.

TCP ensures

```text
Lost Packet

↓

Detected

↓

Sent Again

↓

Correct Order

↓

Complete File
```

---

## How does TCP work?

TCP performs

multiple tasks.

```text
Connection Establishment

↓

Divide Data into Segments

↓

Number Every Segment

↓

Send Segments

↓

Receive ACKs

↓

Resend Lost Segments

↓

Reassemble Data

↓

Close Connection
```

---

# TCP Checksum & Retransmission

## Why TCP Checksum if Data Link Already Has One?

**Data Link Checksum (CRC)**
- Hop-to-hop.
- Verified at every router.
- New frame → New CRC.

**TCP Checksum**
- End-to-end.
- Verified only by the destination.
- Detects corruption across the entire path.

---

## Retransmission

Receiver:
- Checks sequence numbers.
- If a segment is missing, it **does not retransmit**.
- Sends ACKs indicating the last correctly received in-order data.

Sender:
- Detects the missing segment (duplicate ACKs or timeout).
- **Retransmits** the missing segment.

> **Retransmission is always initiated by the sender, not the receiver.**
> 

## Why is TCP designed this way?

The Internet

is

unreliable.

Packets may

```text
Be Lost

Be Delayed

Arrive Out of Order

Be Duplicated
```

TCP

solves

all these problems,

making the Internet

appear

reliable

to applications.

---

# 9. TCP Segment

## What is it?

TCP

does not send

an entire file

at once.

It divides

the data

into

smaller pieces

called

```text
TCP Segments.
```

---

## Why?

Large data

cannot always

travel

as one packet.

Breaking data

into segments

makes

```text
Retransmission easier

Routing easier

Error recovery easier
```

---

## How does it work?

Example

```text
Video File

↓

Segment 1

↓

Segment 2

↓

Segment 3

↓

Segment 4
```

Each segment

gets

its own

TCP Header.

---

## TCP Header (Important Fields)

```text
Source Port

↓

Destination Port

↓

Sequence Number

↓

Acknowledgement Number

↓

Flags

↓

Window Size

↓

Checksum

↓

Data
```

You don't need

to memorize

every field,

but

know

their purpose.

---

# 10. Connection-Oriented Communication

## What is it?

Before

sending data,

TCP first

creates

a connection

between

both devices.

Only then

does

data transfer begin.

---

## Why?

Imagine

calling someone.

You don't

start talking

before

they answer.

Instead,

```text
Call

↓

Answer

↓

Conversation

↓

Hang Up
```

TCP

works

the same way.

---

## How does it work?

```text
Create Connection

↓

Transfer Data

↓

Close Connection
```

---

## Why is it designed this way?

Because

both devices

must agree

that

they are ready

to communicate.

This reduces

errors

and

missing data.

---

# 11. Three-Way Handshake

## What is it?

The

Three-Way Handshake

is the process

used by TCP

to establish

a connection.

---

## Why?

Both sides

must know

that

the other side

is alive

and ready.

---

## How does it work?

### Step 1

Client

asks

to connect.

```text
Client

---- SYN ---->

Server
```

SYN

means

```text
"I want to communicate."
```

---

### Step 2

Server

accepts.

```text
Client

<--- SYN + ACK ----

Server
```

Meaning

```text
"I received your request.

I'm ready too."
```

---

### Step 3

Client confirms.

```text
Client

---- ACK ---->

Server
```

Now

both sides

know

the connection

has been established.

Data transfer

begins.

---

## Mental Model

```text
Client

"Can we talk?"

↓

Server

"Yes."

↓

Client

"Great."

↓

Conversation Starts
```

---

## Why is it designed this way?

One message

isn't enough.

Two messages

still don't guarantee

both sides

know

the connection exists.

Three messages

ensure

both client

and server

know

the connection

has been established.

---

# 12. Sequence Numbers

## What is it?

Every TCP Segment

contains

a

Sequence Number.

It identifies

where

that segment

belongs

in the data stream.

---

## Why?

Packets

may arrive

out of order.

Example

Instead of

```text
1

↓

2

↓

3

↓

4
```

the network

may deliver

```text
3

↓

1

↓

4

↓

2
```

Sequence Numbers

allow

the receiver

to

reassemble

the original data.

---

## How does it work?

Example

```text
Seq = 1

↓

Hello

----------------

Seq = 2

↓

How

----------------

Seq = 3

↓

Are

----------------

Seq = 4

↓

You
```

Even if

they arrive

like

```text
3

1

4

2
```

the receiver

sorts them

using

Sequence Numbers.

---

## Why is it designed this way?

Networks

cannot guarantee

packet order.

TCP

fixes

the order

before

giving data

to the application.

---

# 13. Acknowledgements (ACK)

## What is it?

An ACK

is a message

sent by

the receiver

confirming

that

data

was received.

---

## Why?

Without ACK,

the sender

would never know

whether

the data

arrived safely.

---

## How does it work?

Example

```text
Client

---- Segment ---->

Server

<---- ACK --------
```

ACK means

```text
"I received it."
```

The sender

can now

send

the next segment.

---

## Why is it designed this way?

The Internet

does not

guarantee delivery.

ACKs

allow

TCP

to detect

packet loss.

# 14. Retransmission

## What is it?

Retransmission

means

sending

the same TCP Segment

again

if it

was not received.

---

## Why?

The Internet

is unreliable.

A Segment

may

```text
Be Lost

Be Corrupted

Never Reach

the Receiver
```

Without

Retransmission,

the data

would be incomplete.

---

## How does it work?

Example

```text
Client

---- Segment 1 ---->

Server

<---- ACK ----------

---------------------

Client

---- Segment 2 ---->

❌ Lost

(No ACK)

---------------------

Client waits

↓

Timeout

↓

Resends Segment 2

↓

Server Receives

↓

ACK
```

---

## Why is it designed this way?

Instead of

sending

the entire file

again,

TCP

only resends

the missing segment.

This saves

bandwidth

and

time.

---

# 15. Timeout

## What is it?

A Timeout

is the

maximum time

TCP waits

for an ACK.

If

no ACK arrives,

TCP assumes

the Segment

was lost.

---

## Why?

The sender

cannot wait forever.

It must decide

when

to retransmit.

---

## How does it work?

```text
Send Segment

↓

Wait for ACK

↓

ACK Received?

↓

Yes

↓

Continue

-----------------

No

↓

Timeout

↓

Retransmit
```

---

## Why is it designed this way?

If

the timeout

is

too small,

unnecessary

retransmissions

occur.

If

it is

too large,

communication

becomes slow.

TCP therefore

calculates

a dynamic timeout

based on

network conditions.

---

# 16. Sliding Window

## What is it?

A Sliding Window

allows

multiple TCP Segments

to be sent

before waiting

for ACKs.

---

## Why?

Suppose

we waited

for one ACK

after

every Segment.

```text
Send

↓

Wait

↓

Send

↓

Wait

↓

Send

↓

Wait
```

The network

would remain idle

most of the time.

This wastes

bandwidth.

---

## How does it work?

Suppose

Window Size

is

4.

```text
Send

1

2

3

4

↓

Receive ACK

↓

Window Slides

↓

Send

5

6

7

8
```

Instead of

sending

one segment,

TCP

keeps

the network busy.

---

## Why is it designed this way?

```text
so simply there is wait timefor sending and gettin ack so when you send in window fashion the delay is reduced

```

The Internet

has latency.

Waiting

after every segment

would make

communication

extremely slow.

Sliding Window

improves

throughput

without

sacrificing reliability.

---

## Mental Model

```text
Old Method

Send

↓

Wait

↓

Send

↓

Wait

↓

Send

---------------------

Sliding Window

Send

1

2

3

4

↓

Receive ACKs

↓

Send

5

6

7

8
```

---

# 17. Flow Control

## What is it?

Flow Control

prevents

the sender

from sending data

faster

than

the receiver

can process it.

---

## Why?

Imagine

the sender

can send

1000 MB/s,

but

the receiver

can process

only

100 MB/s.

Without

Flow Control,

the receiver's

buffer

would overflow.

---

## How does it work?

The receiver

advertises

its available

buffer size.

```text
Receiver

↓

Window = 5000 Bytes
```

The sender

never sends

more than

that amount

without

receiving

new ACKs.

---

## Why is it designed this way?

The receiver

knows

its own capacity

better

than the sender.

So

the receiver

controls

the transmission rate.

---

## Mental Model

```text
Receiver

↓

"I'm full.

Slow down."

↓

Sender

↓

Reduces Speed
```

---

# 18. Why is TCP Reliable?

TCP combines

multiple mechanisms.

```text
Connection

↓

Sequence Numbers

↓

Acknowledgements

↓

Retransmission

↓

Timeout

↓

Sliding Window

↓

Flow Control
```

Together,

they ensure

```text
Correct Order

No Missing Data

No Duplicate Data

Reliable Delivery
```

---

# Quick Revision

```text
TCP

↓

Connection-Oriented

↓

Reliable

↓

Three-Way Handshake

↓

Sequence Numbers

↓

Acknowledgements

↓

Timeout

↓

Retransmission

↓

Sliding Window

↓

Flow Control
```

---

# Interview Questions

### Why is TCP reliable?

```text
Because it uses

Sequence Numbers,

Acknowledgements,

Retransmissions,

Timeouts,

Sliding Window,

and

Flow Control.
```

---

### Why do we need Sliding Window?

```text
To improve

network utilization

by sending

multiple segments

before waiting

for ACKs.
```

---

### Why is Flow Control needed?

```text
To prevent

the sender

from overwhelming

the receiver.
```

---

### Difference between Retransmission and Timeout?

```text
Timeout

↓

Waiting period

before assuming

loss.

--------------------

Retransmission

↓

Sending

the lost segment

again.
```

---

# Bridge

So far,

we know

how TCP

achieves

reliable communication.

The next section

covers

UDP,

why it is

connectionless,

why it is

much faster,

and

when it is preferred

over TCP.

# 19. UDP (User Datagram Protocol)

## What is UDP?

UDP

(User Datagram Protocol)

is a

**connectionless**

Transport Layer protocol

that sends data

without

guaranteeing

delivery,

ordering,

or retransmission.

---

## Why do we need UDP?

Not every application

needs

perfect reliability.

Sometimes,

speed

is more important.

Examples

```text
Video Calls

↓

Online Gaming

↓

Live Streaming

↓

DNS
```

Losing

one packet

is acceptable.

Waiting

for retransmission

is not.

---

## How does UDP work?

UDP simply

```text
Application

↓

Add UDP Header

↓

Send Segment

↓

Done
```

No

```text
Connection

ACK

Timeout

Retransmission

Ordering
```

---

## Why is it designed this way?

Every extra feature

adds

delay.

UDP removes

all unnecessary

overhead

to achieve

maximum speed.

---

# 20. UDP Header

## What is it?

Every UDP Segment

contains

a small header.

```text
Source Port

↓

Destination Port

↓

Length

↓

Checksum

↓

Data
```

Its header

is much smaller

than TCP's,

making UDP

faster.

---

## Why?

Smaller header

means

```text
Less Processing

↓

Less Memory

↓

Less Delay
```

---

# 21. Connectionless Communication

## What is it?

UDP

does not

establish

a connection

before sending data.

---

## How does it work?

```text
Sender

↓

Send Packet

↓

Receiver
```

That's all.

---

## Why is it designed this way?

No handshake

means

communication

starts immediately,

reducing latency.

---

# 22. TCP vs UDP

## What is the difference?

| Feature | TCP | UDP |
|----------|-----|-----|
|Connection|Yes|No|
|Reliable|Yes|No|
|Ordered Delivery|Yes|No|
|Retransmission|Yes|No|
|Acknowledgement|Yes|No|
|Speed|Slower|Faster|
|Header Size|Larger|Smaller|

---

## Why do both exist?

Different applications

have different needs.

```text
Need Reliability

↓

TCP

------------------

Need Speed

↓

UDP
```

---

## Real-world Examples

### TCP

```text
HTTPS

↓

File Download

↓

Email

↓

Banking

↓

File Transfer
```

Missing data

is unacceptable.

---

### UDP

```text
Video Call

↓

Gaming

↓

DNS

↓

Live Streaming

↓

VoIP
```

Low latency

is more important

than perfect reliability.

---

# 23. TCP vs UDP Trade-offs

## TCP

### Advantages

```text
Reliable

Ordered

Error Recovery

Flow Control
```

### Disadvantages

```text
Higher Latency

More Overhead

Slower
```

---

## UDP

### Advantages

```text
Very Fast

Low Latency

Simple Header

Low Overhead
```

### Disadvantages

```text
Packet Loss

No Ordering

No Reliability

No Retransmission
```

---

# 24. TCP vs UDP Decision

```text
Need Every Byte?

↓

TCP

------------------

Need Lowest Delay?

↓

UDP
```

---

# 25. End-to-End Communication (Complete Picture)

Suppose

you open

```text
https://google.com
```

The communication

looks like

```text
Chrome

↓

Socket

↓

TCP

↓

IP

↓

Ethernet

↓

Physical Network

↓

Internet

↓

Google Server

↓

Ethernet

↓

IP

↓

TCP

↓

Socket

↓

Google Application
```

Each layer

performs

its own responsibility.

```text
Application

↓

Creates Data

--------------------

Transport

↓

Ports

Reliability

--------------------

Network

↓

Routing

IP Address

--------------------

Data Link

↓

MAC Address

Frame

--------------------

Physical

↓

Bits
```

---

# 26. Quick Revision

```text
Transport Layer

↓

Process-to-Process Communication

↓

Ports

↓

Sockets

↓

Multiplexing

↓

Demultiplexing

↓

TCP

↓

Reliable

↓

Handshake

↓

Sequence Numbers

↓

ACK

↓

Timeout

↓

Retransmission

↓

Sliding Window

↓

Flow Control

-----------------------

UDP

↓

Connectionless

↓

Fast

↓

Low Latency

↓

Best Effort Delivery
```

---

# Interview Questions

### What is the Transport Layer?

```text
Provides

process-to-process

communication

using

Port Numbers.
```

---

### Why do we need Ports?

```text
To identify

the correct

application

on a device.
```

---

### What is a Socket?

```text
A communication endpoint

identified by

IP Address

and

Port Number.
```

---

### Why is TCP reliable?

```text
Connection

↓

Sequence Numbers

↓

ACKs

↓

Timeout

↓

Retransmission

↓

Sliding Window

↓

Flow Control
```

---

### Why is UDP faster?

```text
No Connection

No ACKs

No Retransmission

No Ordering

Smaller Header
```

---

### When should TCP be used?

```text
Whenever

correctness

is more important

than speed.
```

---

### When should UDP be used?

```text
Whenever

low latency

is more important

than perfect reliability.
```

---

# One-Line Mental Model

```text
Transport Layer

↓

Find the Correct Application

using

Port Numbers

↓

TCP

Reliable Delivery

↓

UDP

Fast Delivery
```


# 27. Checksum

## What is it?

A Checksum

is a value

used to detect

whether

data

was corrupted

during transmission.

---

## Why do we need it?

While traveling

across the network,

bits

may change

because of

noise,

hardware faults,

or transmission errors.

The receiver

must know

whether

the received data

is correct.

---

## How does it work?

```text
Sender

↓

Calculate Checksum

↓

Attach Checksum

↓

Send Segment

--------------------

Receiver

↓

Recalculate Checksum

↓

Compare

↓

Match?

↓

Yes

↓

Accept

--------------------

No

↓

Discard
```

---

## Why is it designed this way?

Detecting

corrupted data

is much cheaper

than

processing

incorrect data.

TCP

can then

request

the sender

to retransmit.

---

# 28. MSS (Maximum Segment Size)

## What is it?

MSS

is the

maximum amount

of

application data

that TCP

places

inside

one TCP Segment.

It

does **not**

include

TCP

or

IP headers.

---

## Why do we need MSS?

Suppose

TCP

creates

very large segments.

They may

not fit

inside

the network's

maximum packet size.

So

TCP limits

the size

of each segment.

---

## How does it work?

```text
Large File

↓

Split into

MSS-sized

Segments

↓

Send
```

---

## Why is it designed this way?

Smaller segments

fit

into

network packets

without

fragmentation.

---

# 29. MTU (Maximum Transmission Unit)

## What is it?

MTU

is the

maximum size

of an entire packet

that

a network link

can carry.

It includes

```text
IP Header

+

TCP Header

+

Data
```

---

## Why do we need MTU?

Every network

has

a limit

on

packet size.

If

a packet

is larger

than

the MTU,

it must

either

be fragmented

or

sent

in smaller pieces.

---

## How does it work?

Example

```text
MTU = 1500 Bytes

↓

IP Header

20 Bytes

↓

TCP Header

20 Bytes

↓

Maximum Data

1460 Bytes
```

Therefore,

a common

TCP MSS

is

```text
1460 Bytes
```

---

## Why is it designed this way?

Different

network technologies

support

different

maximum frame sizes.

MTU ensures

packets

fit

on the link.

---

# MSS vs MTU

| MSS | MTU |
|------|-----|
|Application Data Only|Entire Packet|
|TCP Concept|Network Link Concept|
|Doesn't include headers|Includes headers|

---

## Easy Mental Model

```text
MTU

↓

Entire Box

--------------------

MSS

↓

Gift

inside the box
```

---

# 30. Segmentation & Reassembly

## What is it?

Segmentation

means

breaking

large data

into

smaller TCP Segments.

Reassembly

means

joining

them again

at the receiver.

---

## Why do we need it?

A

100 MB file

cannot

be sent

as

one segment.

It must

be divided.

---

## How does it work?

```text
Large File

↓

Segment 1

↓

Segment 2

↓

Segment 3

↓

...

↓

Receiver

↓

Sequence Numbers

↓

Original File
```

---

## Why is it designed this way?

If

one segment

is lost,

TCP

retransmits

only

that segment,

not

the whole file.

---

# 31. Flow Control vs Congestion Control

## Flow Control

### What is it?

Protects

the

receiver.

---

### Why?

The receiver

may process

data

more slowly

than

the sender

can transmit.

---

### How?

The receiver

advertises

its available

window size.

The sender

adjusts

its sending rate.

---

## Congestion Control

### What is it?

Protects

the

network.

---

### Why?

Even if

the receiver

is fast,

routers

may become

overloaded.

This causes

```text
Packet Loss

High Delay

Dropped Packets
```

---

### How?

TCP

reduces

its sending rate

when

it detects

network congestion.

---

## Mental Model

```text
Flow Control

↓

Protect Receiver

--------------------

Congestion Control

↓

Protect Network
```

---

# 32. Buffers

## What is a Buffer?

A Buffer

is a

temporary memory

used

to store

data

before

it is processed.

---

## Why do we need Buffers?

The sender

and

receiver

may operate

at different speeds.

Buffers

temporarily

store data

until

it can

be processed.

---

## How does it work?

```text
Network

↓

Receive Buffer

↓

Application
```

Similarly,

```text
Application

↓

Send Buffer

↓

Network
```

---

## Why is it designed this way?

Without buffers,

small differences

in processing speed

would

cause

packet loss.

---

# 33. TCP States (Interview Level)

For SDE-1,

you only need

the important states.

```text
LISTEN

↓

SYN_SENT

↓

SYN_RECEIVED

↓

ESTABLISHED

↓

FIN_WAIT

↓

CLOSED
```

---

## What do they mean?

```text
LISTEN

↓

Waiting

for a connection.

--------------------

SYN_SENT

↓

Client

requested connection.

--------------------

SYN_RECEIVED

↓

Server

received request.

--------------------

ESTABLISHED

↓

Connection Active.

--------------------

FIN_WAIT

↓

Closing Connection.

--------------------

CLOSED

↓

Connection Finished.
```

---

# Final Quick Revision

```text
Transport Layer

↓

Process-to-Process Communication

↓

Ports

↓

Sockets

↓

Multiplexing

↓

Demultiplexing

↓

TCP

↓

Reliable

↓

Handshake

↓

Sequence Numbers

↓

ACK

↓

Timeout

↓

Retransmission

↓

Sliding Window

↓

Flow Control

↓

Checksum

↓

Segmentation

↓

Reassembly

↓

MSS

↓

MTU

↓

Congestion Control

↓

Buffers

-------------------------

UDP

↓

Connectionless

↓

Fast

↓

Low Latency

↓

Best-Effort Delivery
```

---

# Interview Cheat Sheet

### IP vs Port

```text
IP

↓

Device

----------------

Port

↓

Application
```

---

### MSS vs MTU

```text
MTU

↓

Entire Packet

----------------

MSS

↓

Application Data
```

---

### Flow Control vs Congestion Control

```text
Flow Control

↓

Protect Receiver

----------------

Congestion Control

↓

Protect Network
```

---

### TCP vs UDP

```text
TCP

↓

Reliable

Ordered

Connection-Oriented

---------------------

UDP

↓

Fast

Connectionless

Best-Effort
```
