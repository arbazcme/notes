# CN00 - Introduction to Computer Networks

# Navigation

- [1. Why Do We Need Computer Networks?](#1-why-do-we-need-computer-networks)

- [2. What Is A Network?](#2-what-is-a-network)

- [3. How Does Communication Actually Happen?](#3-how-does-communication-actually-happen)

- [4. What Is A Protocol?](#4-what-is-a-protocol)


- [5. OSI Model Overview](#5-osi-model-overview)


- [6. TCP/IP Model Overview](#6-tcpip-model-overview)


- [7. Encapsulation & Decapsulation](#7-encapsulation--decapsulation)

- [8. Data Units](#8-data-units)

- [9. IP Address vs MAC Address vs Port Number](#9-ip-address-vs-mac-address-vs-port-number)

- [10. Interview Revision](#10-interview-revision)

---

# 1. Why Do We Need Computer Networks?

Suppose

you have

two computers.

Computer A

contains

a movie.

Computer B

needs

that movie.

Question

How can

Computer B

receive it?

One option

is

```text
Copy it

into a Pendrive

↓

Walk

to

Computer B

↓

Paste it.
```

Works.

But imagine

Google.

Millions

of computers

exchange

information

every second.

Clearly,

walking around

with pendrives

is impossible.

We need

a system

that allows

computers

to communicate

automatically.

That system

is called

a

```text
Computer Network.
```

---

## Real World Examples

Whenever

you

open

Instagram,

the photos

do not exist

inside

your phone.

They travel

through

a network.

---

When

you

watch

YouTube,

the video

is streamed

from Google's servers

to

your device.

Again,

through

a network.

---

When

you

play

Valorant,

your PC

continuously exchanges

data

with

the game server.

Again,

a network.

---

Almost

everything

you do

on the Internet

depends on

Computer Networks.

---

## Definition

A Computer Network

is

```text
A collection

of interconnected

devices

that communicate

and

share resources.
```

The devices

may be

```text
Computers

Servers

Phones

Printers

Routers

Switches

IoT Devices
```

---

## Advantages

Networks allow

```text
Resource Sharing

↓

File Sharing

↓

Internet Access

↓

Communication

↓

Cloud Computing

↓

Remote Access
```

Without networks,

modern software

could not exist.

---

# Interview Questions

### What is a Computer Network?

```text
A collection

of interconnected devices

that exchange

information

using

communication protocols.
```

---

### Why are Computer Networks needed?

```text
To allow

devices

to communicate

and

share resources.
```

---

# 2. What Is A Network?

Every network

contains

two basic things.

```text
Nodes

+

Links
```

---

## Nodes

Nodes are

the devices

participating

in communication.

Examples

```text
Laptop

Phone

Server

Router

Printer
```

Every device

connected

to the network

is called

a Node.

---

## Links

Links connect

nodes.

They may be

```text
Ethernet Cable

Fiber Optic

Wi-Fi

Cellular Network
```

No link

means

no communication.

---

## Small Example

```text
Laptop

↓

Wi-Fi

↓

Router

↓

Fiber

↓

Google Server
```

Four nodes.

Three links.

Together,

they form

a network.

---

# 3. How Does Communication Actually Happen?

Communication

always involves

five things.

```text
Sender

↓

Message

↓

Medium

↓

Receiver

↓

Protocol
```

Suppose

you send

"Hello"

using WhatsApp.

Sender

↓

Your Phone

Message

↓

"Hello"

Medium

↓

Internet

Receiver

↓

Friend's Phone

Protocol

↓

Rules

that both devices

understand.

Without

any one

of these,

communication fails.

---

# 4. What Is A Protocol?

Imagine

two people.

One speaks

English.

Another

speaks

Japanese.

Question

Can they communicate?

Not effectively.

They need

a common language.

Computers

have

the same problem.

Every manufacturer

builds

different hardware.

Every operating system

is different.

Still,

they communicate

perfectly.

How?

Using

Protocols.

---

## Definition

A Protocol

is

```text
A set of rules

that governs

communication

between devices.
```

Protocols define

```text
Message Format

↓

Timing

↓

Error Handling

↓

Addressing

↓

Delivery Rules
```

Every device

following

the same protocol

can communicate.

---

## Examples

```text
HTTP

↓

Web Pages

----------------

HTTPS

↓

Secure Web Pages

----------------

TCP

↓

Reliable Delivery

----------------

UDP

↓

Fast Delivery

----------------

DNS

↓

Website Name

↓

IP Address
```

We'll study

each protocol

later.

---

# Interview Question

### What is a Protocol?

```text
A set of rules

that defines

how devices

communicate

over a network.
```

---

# 5. OSI Model Overview

Question

Networking

contains

many problems.

Who should

handle

addressing?

Who should

handle

encryption?

Who should

handle

routing?

If

every protocol

did everything,

networking

would become

impossible.

The solution

is

```text
Layers.
```

---

## The Seven Layers

```text
7 Application

6 Presentation

5 Session

4 Transport

3 Network

2 Data Link

1 Physical
```

Each layer

has

one responsibility.

It provides

services

to

the layer above

and

uses

services

from

the layer below.

---

## Easy Memory Trick

```text
All

People

Seem

To

Need

Data

Processing
```

---

## Responsibilities

```text
Application

↓

User Services

----------------

Presentation

↓

Encryption

Compression

----------------

Session

↓

Connection Management

----------------

Transport

↓

Reliable Delivery

----------------

Network

↓

Routing

IP Address

----------------

Data Link

↓

MAC Address

Frames

----------------

Physical

↓

Bits

Signals

Wire
```

Don't worry.

Each layer

will get

its own chapter.

---

# Why Layers?

Imagine

building

a car.

One engineer

designs

the engine.

Another

designs

the brakes.

Another

designs

the steering.

Everyone

works

independently.

Networking

uses

exactly

the same idea.

---

# 6. TCP/IP Model

Real networks

do not

directly implement

OSI.

Instead,

they use

TCP/IP.

---

## Layers

```text
Application

Transport

Internet

Network Access
```

Much simpler.

---

## Mapping

```text
OSI

Application

Presentation

Session

↓

TCP/IP

Application

----------------

OSI

Transport

↓

TCP/IP

Transport

----------------

OSI

Network

↓

TCP/IP

Internet

----------------

OSI

Data Link

Physical

↓

TCP/IP

Network Access
```

---

## Interview Question

### Why do we study OSI if TCP/IP is used?

```text
OSI

is a

Reference Model.

TCP/IP

is the

Practical Implementation.
```

---

# 7. Encapsulation & Decapsulation

Suppose

you send

a message.

```text
Hello
```

Question

Does the network

send only

"Hello"?

No.

Each layer

adds

its own information.

Application

↓

Message

↓

Transport

adds

TCP Header

↓

Network

adds

IP Header

↓

Data Link

adds

MAC Header

↓

Physical

sends

Bits.

This process

is called

```text
Encapsulation.
```

---

At the receiver,

everything happens

in reverse.

Headers

are removed

one by one.

This is

```text
Decapsulation.
```

---

# 8. Data Units

Different layers

use

different names.

```text
Application

↓

Message

----------------

Transport

↓

Segment

(TCP)

or

Datagram

(UDP)

----------------

Network

↓

Packet

----------------

Data Link

↓

Frame

----------------

Physical

↓

Bits
```

One of the

most common

interview questions.

---

# 9. IP Address vs MAC Address vs Port

Students

often confuse

these three.

Think of

sending

a courier.

Your House

↓

IP Address

Which building?

----------------

Your Name

↓

MAC Address

Which device?

----------------

Room Number

↓

Port Number

Which application?

---

More accurately

```text
IP Address

↓

Identifies

a device

across

different networks.

----------------

MAC Address

↓

Identifies

the Network Card

inside

a local network.

----------------

Port Number

↓

Identifies

the application

inside

the device.
```

We'll study

all three

deeply

later.

---

# Mental Model

Sending

a WhatsApp message

looks like

```text
You

↓

Application

↓

TCP

↓

IP

↓

Wi-Fi

↓

Internet

↓

Friend's Wi-Fi

↓

Friend's IP

↓

Friend's TCP

↓

WhatsApp
```

Every layer

adds

or removes

its own information.

---

```text
IP Address
↓
Logical Address

MAC Address
↓
Physical (Hardware) Address

Port Number
↓
Process/Application Identifier

```

# Interview Revision

### Network

```text
Connected devices

sharing information.
```

---

### Protocol

```text
Rules

for communication.
```

---

### OSI

```text
Reference Model

with

7 Layers.
```

---

### TCP/IP

```text
Practical Model

used

on the Internet.
```

---

### Encapsulation

```text
Adding

Headers

while sending.
```

---

### Decapsulation

```text
Removing

Headers

while receiving.
```

---

### Data Units

```text
Message

↓

Segment

↓

Packet

↓

Frame

↓

Bits
```

---

### Addresses

```text
IP

↓

Device

across networks

----------------

MAC

↓

Network Card

----------------

Port

↓

Application
```

# Bridge

Everything

from this chapter

answered

one question:

```text
How does communication

generally happen?
```

Next,

we'll begin

the actual journey

of a packet,

starting from

the

**Physical Layer**

and

**Data Link Layer**,

where bits

first begin

their travel

across the network.
