# CN01 - Physical Layer & Data Link Layer

# Navigation

- [1. Why Do We Need Layers? (Revisited)](#1-why-do-we-need-layers-revisited)
- [2. Physical Layer](#2-physical-layer)
  - [Responsibilities](#responsibilities)
  - [Example](#example)
  - [Transmission Media](#transmission-media)
    - [Guided Media](#guided-media)
    - [Twisted Pair](#twisted-pair)
    - [Coaxial Cable](#coaxial-cable)
    - [Fiber Optic Cable](#fiber-optic-cable)
    - [Unguided Media](#unguided-media)
  - [Bandwidth](#bandwidth)
  - [Latency](#latency)
  - [Throughput](#throughput)
  - [Half Duplex vs Full Duplex](#half-duplex-vs-full-duplex)
  - [Interview Questions](#interview-questions)

- [3. Data Link Layer](#3-data-link-layer)
  - [Responsibilities](#responsibilities-1)
  - [Framing](#framing)
  - [Physical vs Data Link](#physical-vs-data-link)
  - [Bridge](#bridge)
- [4. Network Devices](#4-network-devices)
  - [Hub](#hub)
  - [Switch](#switch)
  - [Router (Introduction)](#router-introduction)
  - [Hub vs Switch](#hub-vs-switch)

- [5. MAC Address](#5-mac-address)
  - [What is a MAC Address?](#what-is-a-mac-address)
  - [MAC vs IP Address](#mac-vs-ip-address)
  - [Interview Questions](#interview-questions-1)

- [6. ARP (Address Resolution Protocol)](#6-arp-address-resolution-protocol)
  - [Why ARP is Needed](#why-arp-is-needed)
  - [How ARP Works](#how-arp-works)
  - [ARP Cache](#arp-cache)
  - [Interview Questions](#interview-questions-2)

- [7. Error Detection](#7-error-detection)
  - [Parity Bit](#parity-bit)
  - [Checksum](#checksum)
  - [CRC (Concept)](#crc-concept)
  - [Interview Questions](#interview-questions-3)

- [8. Ethernet](#8-ethernet)
  - [Ethernet Frame](#ethernet-frame)
  - [CSMA/CD (Idea)](#csmacd-idea)
  - [Interview Questions](#interview-questions-4)

- [9. Chapter Revision](#9-chapter-revision)



# How IP and MAC Work Together (My Mental Model)

## My Understanding

A router **X** wants to send data to a specific device in another router's local network (**Y**).

Since the destination device's **MAC address is only valid inside Router Y's local network**, Router X **cannot use that MAC**.

Instead, Router X sends:

- **Destination IP** (of the final device)
- **Data**

When Router Y receives the packet, it:

1. Looks at the **Destination IP**.
2. Finds which local device owns that private IP.
3. Finds that device's **MAC address** (using ARP in practice).
4. Creates a frame with that MAC address and delivers the data.

This is why **IP and MAC work together**.

---

# Diagram

```text
                Internet

             Public IP
                 │
             Router X
       ┌─────────┴─────────┐
       A                   B
IP: 192.168.1.10     IP: 192.168.1.11
MAC: A1              MAC: B1

                │
          (Internet)
                │

             Router Y
       ┌─────────┴─────────┐
       D                   E
IP: 192.168.2.20     IP: 192.168.2.21
MAC: D1              MAC: E1
```

---

# Example

Suppose **A wants to send data to E.**

## Step 1

A creates a packet.

```text
Destination IP = 192.168.2.21
```

---

## Step 2

Router X receives the packet.

It checks:

> "This destination IP is not in my local network."

So it forwards the packet towards Router Y.

Notice:

- ✅ Router X **does not need E's MAC address.**
- ✅ It only needs E's **IP address**.

---

## Step 3

Router Y receives the packet.

It checks:

```text
Destination IP = 192.168.2.21
```

Router Y says:

> "This IP belongs to device E."

---

## Step 4

Router Y finds E's MAC address (using **ARP**).

Then it creates a local frame.

```text
Destination MAC = E1
```

The frame is sent to E.

---

# Why Do We Need Public and Private IP?

## 🌍 Public IP

A **Public IP** identifies your **entire home/office network** on the Internet.

Example:

```text
49.204.100.50
```

The Internet only knows this address.

---

## 🏠 Private IP

A **Private IP** identifies an **individual device inside your local network**.

Example:

```text
Laptop  → 192.168.1.10
Phone   → 192.168.1.11
TV      → 192.168.1.12
```

Without private IPs, the router would not know **which internal device** a packet is meant for.

---

## 💻 MAC Address

A **MAC Address** identifies the **actual network hardware**.

It is **only used inside the local network (LAN)** to deliver the frame to the correct device.

MAC addresses are **not used across the Internet**.

---

# One-Line Summary

- 🌍 **Public IP** → Identifies your **network** on the Internet.
- 🏠 **Private IP** → Identifies a **specific device** inside that network.
- 💻 **MAC Address** → Identifies the **actual hardware** on the local network for final delivery.

---

# Final Mental Model

> **Routers communicate using IP addresses. Once the packet reaches the destination router, that router uses the destination private IP to identify the correct local device, finds its MAC address, creates a frame, and delivers the data.**



# 1. Why Do We Need Layers? (Revisited)

Suppose

Alice

wants to send

a photo

to Bob.

Question

Should

one giant program

perform

all these tasks?

```text
Compress Image

↓

Encrypt Image

↓

Find Bob

↓

Choose Route

↓

Detect Errors

↓

Convert

to Electrical Signals

↓

Send
```

Impossible.

Too much work.

Instead,

Networking divides

the problem

into

layers.

Each layer

has

one responsibility.

```text
Application

↓

Transport

↓

Network

↓

Data Link

↓

Physical
```

Each layer

solves

its own problem.

This makes

networking

modular,

easy to improve,

and easy to debug.

---

# 2. Physical Layer

The Physical Layer

is

Layer 1

of the OSI Model.

Question

What does it actually do?

Very simple.

It is responsible

for moving

```text
Bits

0

1

0

1

0

1
```

from one device

to another.

It knows nothing

about

files,

packets,

IP,

or

TCP.

It only understands

```text
Electrical Signals

Light Signals

Radio Waves
```

---

## Responsibilities

The Physical Layer

is responsible for

```text
Sending Bits

↓

Receiving Bits

↓

Electrical Signals

↓

Optical Signals

↓

Wireless Signals

↓

Cable Specifications

↓

Voltage Levels

↓

Data Rate
```

It does **not**

understand

what those bits

actually mean.

---

## Example

Suppose

you send

```text
Hello
```

Eventually,

the Physical Layer

converts everything into

```text
01001000

01100101

01101100

01101100

01101111
```

Then,

those bits become

electrical pulses

inside

an Ethernet cable

or

radio waves

through Wi-Fi.

---

# Transmission Media

Question

How do

these bits

travel?

They require

a medium.

There are

two major types.

---

## Guided Media

Signals travel

through

a physical cable.

Examples

```text
Twisted Pair Cable

↓

Coaxial Cable

↓

Fiber Optic Cable
```

---

### Twisted Pair

Most common.

Used in

LANs

and

Ethernet.

Advantages

```text
Cheap

Easy Installation

Flexible
```

Disadvantages

```text
Short Distance

Affected by Noise
```

---

### Coaxial Cable

Used earlier

for Cable TV

and

older networks.

Better shielding

than

Twisted Pair.

Less common today.

---

### Fiber Optic Cable

Uses

light

instead of

electricity.

Advantages

```text
Very High Speed

Very Long Distance

Immune to EMI

High Bandwidth
```

Disadvantages

```text
Expensive

Harder to Install
```

This is what

Internet Service Providers

primarily use

for backbone networks.

---

## Unguided Media

No cable.

Signals travel

through air.

Examples

```text
Wi-Fi

Bluetooth

Satellite

Cellular

Radio
```

---

# Bandwidth

Question

Suppose

one road

has

2 lanes.

Another

has

10 lanes.

Which carries

more cars?

Obviously

10 lanes.

Networking

uses

the same idea.

Bandwidth means

```text
Maximum amount

of data

that can be transmitted

per second.
```

Usually measured in

```text
Mbps

Gbps
```

Higher Bandwidth

↓

More data

can travel

every second.

---

# Latency

Students

often confuse

Bandwidth

and

Latency.

Bandwidth

asks

```text
How much?
```

Latency asks

```text
How long?
```

Example

Suppose

a packet

takes

50 milliseconds

to reach

Google.

Then

```text
Latency

=

50 ms
```

Lower latency

is better,

especially for

```text
Gaming

Video Calls

Stock Trading
```

---

# Throughput

Bandwidth

is

theoretical maximum.

Throughput

is

the actual

data transferred.

Example

Internet Plan

```text
100 Mbps
```

Actual Speed

```text
72 Mbps
```

Bandwidth

↓

100 Mbps

Throughput

↓

72 Mbps

Due to

noise,

traffic,

and congestion.

---

# Half Duplex vs Full Duplex

Suppose

two people

share

one walkie-talkie.

Only

one person

can speak

at a time.

This is

```text
Half Duplex.
```

Examples

```text
Walkie Talkie
```

---

Now suppose

both people

use phones.

Both can

talk

simultaneously.

This is

```text
Full Duplex.
```

Examples

```text
Phone Calls

Modern Ethernet
```

---

# Interview Questions

### What does

the Physical Layer do?

```text
Transfers Bits

using

electrical,

optical,

or wireless signals.
```

---

### Bandwidth vs Latency?

```text
Bandwidth

↓

Amount of Data

Latency

↓

Time Taken
```

---

### Throughput?

```text
Actual

data transferred.
```

---

### Fiber Optic Advantage?

```text
Very High Speed

Long Distance

Immune to EMI.
```

---

# 3. Data Link Layer

The Physical Layer

can send bits.

Question

What if

some bits

change

during transmission?

What if

the receiver

doesn't know

where

one message ends

and

another begins?

These problems

are solved by

the

```text
Data Link Layer.
```

---

## Responsibilities

The Data Link Layer

is responsible for

```text
Framing

↓

MAC Addressing

↓

Error Detection

↓

Flow Control

↓

Reliable

Node-to-Node Delivery
```

Notice

Node-to-Node.

Not

End-to-End.

We'll study

End-to-End

later

using TCP.

---

# Framing

Suppose

the sender

transmits

```text
101001001110010101010010010
```

Question

Where

does

Message 1

end?

Where

does

Message 2

begin?

Impossible

to know.

The Data Link Layer

groups

bits

into

Frames.

Example

```text
Frame 1

↓

Header

+

Data

+

Trailer
```

Instead of

a random stream

of bits,

the receiver

gets

organized units

called

Frames.

---

# Physical vs Data Link

Students

often confuse

these two.

Physical Layer

```text
Moves Bits.
```

Data Link Layer

```text
Organizes Bits

into Frames,

adds addresses,

detects errors.
```

Think

of shipping.

Physical Layer

↓

Truck

moves boxes.

Data Link Layer

↓

Packs

the boxes

properly,

labels them,

checks damage.

---

# Bridge

So far,

we know

how

bits

travel

through wires

and

how

they become

Frames.

Question

How do

multiple computers

share

the same network?

If

10 computers

are connected,

who decides

where

a Frame

should go?

Should everyone

receive it?

Or only

the intended device?

To answer that,

we need

network devices like

```text
Hub

Bridge

Switch

Router
```

These devices

form

the backbone

of modern networks.

# 4. Network Devices

So far,

we know

that the Physical Layer

moves bits,

and the Data Link Layer

packages those bits

into Frames.

Question

Once a Frame

is created,

how does it reach

the correct computer?

This is where

network devices

come into play.

The most important ones are

```text
Hub

↓

Switch

↓

Router
```

---

## Hub

A Hub

is the simplest

network device.

Suppose

five computers

are connected

to one Hub.

```text
     Hub

 / / | \ \

A B C D E
```

Now,

Computer A

sends a Frame

to Computer C.

Question

What does

the Hub do?

```text
It sends

the Frame

to EVERY computer.
```

Every computer

receives it.

Only

Computer C

accepts it.

The others

simply ignore it.

---

### Problems with Hub

- Wastes bandwidth.
- Everyone receives every Frame.
- More collisions.
- Less efficient.

Because of this,

Hubs are

rarely used today.

---

## Switch

A Switch

solves

the Hub's problem.

Instead of

sending

the Frame

to everyone,

it sends it

only

to the required computer.

How?

Using

```text
MAC Addresses.
```

The Switch

maintains

a MAC Address Table

that tells it

which MAC Address

is connected

to which port.

Example

| MAC Address | Port |
|-------------|------|
|AA:11|1|
|BB:22|2|
|CC:33|3|

Suppose

the destination

MAC Address

is

```text
CC:33
```

The Switch

checks

its table

and forwards

the Frame

only

to Port 3.

Much faster

than a Hub.

---

## Router (Introduction)

Question

Can a Switch

send data

to Google?

```text
No.
```

A Switch

works only

inside

a Local Area Network (LAN).

To communicate

between

different networks,

we need

a

```text
Router.
```

A Router

uses

IP Addresses

to forward

Packets

between

different networks.

We'll study

Routers

deeply

in

the Network Layer.

---

```text
switch have mapping of mac : port mapping in simple and local communication , and router can communicate
with other routers (other network ) and can also maintain its local connected devices MAC mapping !
```

## Hub vs Switch

| Hub | Switch |
|------|---------|
|Physical Layer|Data Link Layer|
|Broadcasts everything|Forwards only where needed|
|No MAC Table|Uses MAC Table|
|Slow|Fast|

---

# 5. MAC Address

Every

Network Interface Card (NIC)

has

a unique identifier

called

a

```text
MAC Address
```

MAC stands for

```text
Media Access Control.
```

A MAC Address

is

a

48-bit

hardware address.

Example

```text
00:1A:2B:3C:4D:5E
```

It is assigned

by

the manufacturer

and identifies

the network card.

---

## Why Do We Need MAC Addresses?

Suppose

four computers

are connected

to a Switch.

Question

How does

the Switch

know

where

to send

a Frame?

It looks at

the

Destination MAC Address

inside

the Frame.

Every Frame

contains

```text
Source MAC

↓

Destination MAC
```

Using

the Destination MAC,

the Switch

forwards

the Frame

to the correct computer.

---

## MAC vs IP Address

Students

often confuse

these.

| MAC Address | IP Address |
|-------------|------------|
|Physical Address|Logical Address|
|Identifies Network Card|Identifies Device on a Network|
|Usually Permanent|May Change|
|Used inside LAN|Used across Networks|

Easy way

to remember

```text
MAC

↓

Who are you?

(IP-independent)

----------------

IP

↓

Where are you?
```

---

# 6. ARP (Address Resolution Protocol)

Question

Suppose

you know

someone's

IP Address.

How do you

find

their

MAC Address?

This is exactly

what

ARP

does.

---

## Why Is ARP Needed?

A Router

uses

IP Addresses.

A Switch

uses

MAC Addresses.

Suppose

your computer

wants to send

a Frame.

It knows

the destination IP,

but the Switch

needs

a MAC Address.

Therefore,

the sender

must first

discover

the MAC Address.

---

## How ARP Works

Suppose

Computer A

wants to send

data

to Computer B.

It knows

```text
IP Address

↓

192.168.1.20
```

but does not know

its MAC Address.

Step 1

Computer A

broadcasts

an

ARP Request.

```text
Who has

192.168.1.20?
```

Every computer

receives it.

Step 2

Only

Computer B

replies.

```text
I have it.

My MAC is

AA:BB:CC:DD
```

Step 3

Computer A

stores

this mapping

and sends

the Frame.

---

## ARP Cache

Instead of

asking

every time,

the computer

stores

the result

inside

an

ARP Cache.

Example

| IP | MAC |
|----|-----|
|192.168.1.20|AA:BB:CC:DD|

Future communication

uses

this cache,

making

communication faster.

---

# Switch vs Router & Hosts

## Switch
- Maintains a **MAC → Port** table.
- **Floods** broadcast frames (e.g., ARP Requests) to all ports except the one it was received on.
- Forwards unicast frames to the correct port using the MAC → Port table.

> **Flooding** = Sending a frame to all ports except the incoming port.
> Learn it in detail later when studying **Switching / Ethernet**.

---

## Router & Hosts
- Maintain an **ARP Cache** (**IP → MAC**).
- Use ARP to discover the MAC address corresponding to an IP address.

# Interview Questions

### What does ARP do?

```text
Maps

an IP Address

to

a MAC Address.
```

---

### Is ARP used

inside

or outside

a LAN?

```text
Inside

a Local Network.
```

---

# 7. Error Detection

During transmission,

bits

may change

because of

noise,

interference,

or hardware problems.

The Data Link Layer

must detect

these errors.

---

## Parity Bit

The simplest

error detection

technique.

One extra bit

is added

to detect

single-bit errors.

Very simple,

but

not very reliable.

---

## Checksum

Instead of

adding

one bit,

the sender

computes

a numerical value

from

the data.

The receiver

computes it again.

If the values

do not match,

an error

occurred.

---

## CRC (Cyclic Redundancy Check)

CRC

is the

most common

error detection

technique

used

in Ethernet.

Instead of

simple addition,

CRC

uses

a mathematical algorithm

to detect

errors.

For interviews,

remember

only this:

```text
Parity

↓

Simple

----------------

Checksum

↓

Better

----------------

CRC

↓

Most Reliable

(Common in Ethernet)
```

No need

to learn

the polynomial

calculations

for SDE-1.

---
# 8. Ethernet

Ethernet is the most widely used

**Data Link Layer technology**

for communication inside a

**Local Area Network (LAN).**

Its main job is to

encapsulate an IP Packet into an

**Ethernet Frame**

so it can be transmitted over the local network.

---

## Ethernet Frame

```text
Destination MAC

↓

Source MAC

↓

Type

↓

Data (IP Packet)

↓

CRC
```

- **Destination MAC** → Next device on the current LAN.
- **Source MAC** → Sender's MAC Address.
- **Type** → Type of data (e.g., IPv4, IPv6).
- **Data** → Usually an IP Packet.
- **CRC** → Error detection.


```text
A more accurate version is:

Frame
------
Destination MAC  ← Next hop
Source MAC
Type
Payload (IP Packet)
CRC

Inside the payload (the IP packet) are:

IP Packet
---------
Source IP
Destination IP
TCP/UDP
Application Data

So the destination IP is not in the Ethernet header—it's inside the IP packet (the payload of the Ethernet frame).

```

---

## Mental Model

```text
IP
↓

Final Destination

--------------------

Ethernet
↓

Delivers the Packet
to the next device
on the current LAN
using MAC Addresses.
```

# IP vs MAC - Mental Model

## Core Idea

- **Switch** → Uses **MAC Addresses** for communication **within a local network (LAN)**.
- **Router** → Uses **IP Addresses** to communicate **between different networks**.

Switch:
- MAC known → Forward to correct port.
- ARP Request → Broadcast it.
- ARP Reply → Forward it back.

Host/Router:
- Maintains ARP Cache (IP → MAC).

Hub just broadcasts everytime and misses , MAC known → Forward to correct port. and broadcast even this , - ARP Reply → Forward it back.

---

## How They Work Together

Suppose device **A** wants to send data to device **E** in another network.

1. A creates a packet with **Destination IP = E**.
2. Router X forwards the packet towards Router Y using the **destination IP**.
3. Router X **does not need E's MAC address**.
4. Router Y receives the packet.
5. Router Y sees that E's IP belongs to its local network.
6. Router Y finds E's **MAC address** (using ARP).
7. Router Y creates a frame with **Destination MAC = E** and delivers it.

---

## Why Both Public & Private IP?

### Public IP
- Identifies your **entire network** on the Internet.

### Private IP
- Identifies a **specific device** inside your local network.

Without private IPs, the router wouldn't know **which local device** should receive the packet.

---

## MAC Address

- Identifies the **actual network interface (hardware)**.
- Used **only on the current local network (LAN)**.
- MAC addresses **change at every hop**.

---

## Communication Flow

```text
Device ---- Switch ---- Router ---- Switch ---- Router ---- Switch ---- Device
             MAC          IP          MAC         IP          MAC
```

Think of it as:

- **Switch → "Which MAC should receive this frame?"**
- **Router → "Which IP should this packet ultimately reach?"**

---

## One-Line Summary

- 🌍 **Public IP** → Identifies your network on the Internet.
- 🏠 **Private IP** → Identifies a device inside your network.
- 💻 **MAC** → Used for final/local delivery on the current LAN.


## CSMA/CD (Idea)

Older Ethernet

used

a technique

called

```text
CSMA/CD
```

to reduce

collisions.

Before sending,

a computer

first checked

whether

the cable

was already busy.

If

two computers

still transmitted

at the same time,

a collision

occurred.

They both

waited

for a random time

and

tried again.

Modern

full-duplex

Ethernet

rarely uses

CSMA/CD,

but interviewers

may still ask

its purpose.

---

# Interview Questions

### Hub vs Switch?

```text
Hub

Broadcasts

Everything.

----------------

Switch

Uses

MAC Addresses

to forward Frames.
```

---

### Why is

ARP needed?

```text
To obtain

the MAC Address

from

an IP Address.
```

---

### What is

a MAC Address?

```text
Unique

Hardware Address

of

a Network Interface Card.
```

---

### Which device

uses

MAC Addresses?

```text
Switch.
```

---

### Which device

uses

IP Addresses?

```text
Router.
```

---

### Which error detection

technique

is used

in Ethernet?

```text
CRC.
```

---

# 9. Chapter Revision

```text
Physical Layer
↓

Moves Bits

----------------

Data Link Layer
↓

Frames

↓

MAC Address

↓

Error Detection

↓

Node-to-Node Delivery

----------------

Hub
↓

Broadcasts Everything

----------------

Switch
↓

Uses MAC Address

↓

Forwards Frames

----------------

Router
↓

Connects Different Networks

↓

Uses IP Address

----------------

ARP
↓

IP Address

↓

MAC Address

----------------

Ethernet
↓

Most Common LAN Technology

----------------

CRC
↓

Detects Errors
```

# End of CN01

You now understand

how

data travels

inside

a Local Area Network.

The next chapter,

**CN02 – Network Layer**,

answers the next big question:

```text
How does

a packet

travel

from

your laptop

in India

to

Google's servers

in another country?
```

That is where

IP,

Routing,

Subnetting,

NAT,

DHCP,

ICMP,

and Routers

come together.
