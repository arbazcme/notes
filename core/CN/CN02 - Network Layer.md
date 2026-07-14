# CN02 - Network Layer

# Navigation

- [1. Why Do We Need the Network Layer?](#1-why-do-we-need-the-network-layer)
- [2. IP Address](#2-ip-address)
- [3. Subnetting](#3-subnetting)
- [4. Router](#4-router)
- [5. Routing](#5-routing)
- [6. NAT (Network Address Translation)](#6-nat-network-address-translation)
- [7. DHCP (Dynamic Host Configuration Protocol)](#7-dhcp-dynamic-host-configuration-protocol)
- [8. ICMP (Internet Control Message Protocol)](#8-icmp-internet-control-message-protocol)
- [9. IPv4 vs IPv6 (Quick Revision)](#9-ipv4-vs-ipv6-quick-revision)
- [10. Chapter Revision](#10-chapter-revision)
- [Interview Rapid Fire](#interview-rapid-fire)

---

# 1. Why Do We Need the Network Layer?

In the previous chapter,

we learned

about the

Data Link Layer.

It can successfully

send Frames

from one computer

to another

inside

the same

Local Area Network (LAN).

Question:

Is that enough

to build

the Internet?

```text
No.
```

Let's understand why.

---

## The Problem

Suppose

your laptop

and your friend's laptop

are connected

to

the same Wi-Fi.

```text
Laptop A

↓

Wi-Fi Router

↓

Laptop B
```

Can they

communicate?

```text
Yes.
```

The Data Link Layer

using

MAC Addresses

can deliver

Frames

between them.

Everything works.

---

Now suppose

you open

```text
www.google.com
```

Question:

Where is

Google?

Certainly

not inside

your home.

Google's servers

may be

hundreds

or even

thousands

of kilometers away.

Question:

Can your

Switch

or

Data Link Layer

directly

send data

to Google?

```text
No.
```

Why?

Because

they only know

devices

inside

their own

Local Network.

They have

no knowledge

of

the rest

of the Internet.

---

## Why Data Link Layer Isn't Enough

Think

of your home.

Suppose

you want

to send

a book

to your brother.

If

he lives

in the same house,

you simply

walk

to his room.

Easy.

Now suppose

he lives

in another city.

Walking

inside your house

won't help.

You now need

```text
Roads

↓

Cities

↓

Highways

↓

Postal System
```

Similarly,

the Data Link Layer

works only

inside

one Local Network.

To travel

between

different networks,

we need

another layer.

That layer

is called

the

```text
Network Layer.
```

---

## What Does the Network Layer Do?

The Network Layer

has

one primary responsibility.

```text
Deliver Packets

from

one Network

to

another Network.
```

Notice

the difference.

```text
Data Link Layer

↓

Node-to-Node Delivery

(Inside One LAN)

-------------------------

Network Layer

↓

End-to-End Network Delivery

(Between Different Networks)
```

The Network Layer

doesn't care

what the data is.

It only asks

questions like

```text
Where should

this Packet go?

↓

Which Router

should receive it next?

↓

What is

the best path?
```

This process

is called

```text
Routing.
```

---

## Real World Analogy

Suppose

you book

a courier.

The courier company

doesn't care

whether

the package contains

books,

clothes,

or a laptop.

It only needs

one thing.

```text
Destination Address.
```

Using that address,

it forwards

the parcel

from

city

↓

state

↓

country

↓

destination.

The Network Layer

works

the same way.

Instead of

a postal address,

it uses

an

```text
IP Address.
```

---

## Why Can't We Use MAC Addresses?

This is

one of the most common

interview questions.

Suppose

your laptop

has

the MAC Address

```text
AA:BB:CC:DD:EE:FF
```

Question:

Can Google

find your laptop

using

this MAC Address?

```text
No.
```

Why?

Because

MAC Addresses

work only

inside

a Local Network.

Every time

a Packet

passes through

a Router,

the Frame

is removed,

a new Frame

is created,

and

new MAC Addresses

are used.

Therefore,

MAC Addresses

change

at every hop.

But

the destination

IP Address

remains

the same

throughout

the journey.

That's why

Routers

use

IP Addresses,

not

MAC Addresses.

---

## Switch vs Router

Students

often confuse

these two devices.

Think

of a college.

Inside

your classroom,

everyone

knows

the roll numbers

of students.

But

suppose

you want

to send

a letter

to another college.

Roll numbers

are useless.

You need

the complete

postal address.

Networking

works

exactly

the same way.

```text
Switch

↓

Uses MAC Address

↓

Works Inside

One LAN

----------------------

Router

↓

Uses IP Address

↓

Connects

Different Networks
```

---

## Responsibilities of the Network Layer

The Network Layer

is responsible for

```text
Logical Addressing

↓

Routing

↓

Packet Forwarding

↓

Path Selection

↓

Inter-Network Communication
```

Notice

that

it does

NOT

provide

reliable delivery.

Reliability

belongs

to

the

Transport Layer

(TCP),

which we'll study

later.

---

## Layer Comparison

By now,

the first

three layers

should make sense.

```text
Physical Layer

↓

Moves Bits

----------------------

Data Link Layer

↓

Moves Frames

Inside One LAN

Using MAC Addresses

----------------------

Network Layer

↓

Moves Packets

Between Networks

Using IP Addresses
```

Every layer

solves

a bigger problem

than

the previous one.

---

## Interview Questions

### Why do we need the Network Layer?

```text
Because

the Data Link Layer

can communicate

only inside

one Local Network.

The Network Layer

allows communication

between

different Networks.
```

---

### Which device works at the Network Layer?

```text
Router.
```

---

### Which address does the Network Layer use?

```text
IP Address.
```

---

### Which address does the Data Link Layer use?

```text
MAC Address.
```

---

### What is Routing?

```text
The process

of selecting

the path

for a Packet

to travel

from

the source

to

the destination.
```

---

# Bridge

Now,

we know

why

the Network Layer

exists.

Question:

If Routers

use

IP Addresses,

what exactly

is

an

IP Address?

That is

the next topic,

and it forms

the foundation

of the Internet.

# 2. IP Address

We learned

that

the Network Layer

uses

```text
IP Addresses
```

instead of

MAC Addresses.

Question

What exactly

is

an IP Address?

---

## What Is An IP Address?

An IP Address

(Internet Protocol Address)

is

a

logical address

used to

identify

a device

on a network.

Think

of

sending

a courier.

Every house

has

an address.

Without it,

the courier

has no idea

where to deliver

the package.

Similarly,

every device

connected

to the Internet

needs

an

IP Address.

Without it,

routers

would never know

where

to send

the Packet.

---

## Why Can't We Use Names?

Question

Why doesn't

the Internet

simply use

```text
www.google.com
```

instead of

IP Addresses?

Because

Routers

cannot understand

human-readable names.

Routers

only understand

numbers.

Example

```text
google.com

↓

142.250.183.110
```

DNS

(which we'll study later)

converts

the website name

into

an IP Address.

After that,

routers

forward

the Packet

using

the IP Address.

---

# IPv4

The most common

IP Address format

is

IPv4.

Example

```text
192.168.1.10
```

Notice

four numbers

separated

by dots.

Each number

ranges from

```text
0

to

255
```

This is because

each part

uses

8 bits.

Therefore

IPv4 contains

```text
32 bits

=

4 Bytes
```

---

## Example

```text
192.168.1.10

↓

192

↓

168

↓

1

↓

10
```

Every part

is called

an

```text
Octet.
```

There are

4 octets

in IPv4.

---

# Why Was IPv4 Not Enough?

When

the Internet

was created,

32 bits

seemed

more than enough.

A 32-bit address

can generate

about

```text
4.3 Billion

unique addresses.
```

Question

Isn't that enough?

Not anymore.

Think about

today.

One person

may own

```text
Laptop

Phone

Tablet

Smart TV

Smart Watch
```

Every device

needs

an IP Address.

Now multiply

that

by

billions

of people.

IPv4 addresses

started

running out.

This led

to

IPv6.

---

# IPv6

IPv6

uses

128 bits

instead of

32 bits.

Example

```text
2001:0db8:85a3:0000

:0000:8a2e:0370:7334
```

Looks scary.

Don't memorize it.

For interviews,

remember

only

```text
IPv4

↓

32 bits

----------------

IPv6

↓

128 bits

↓

Much larger

address space.
```

---

## Why IPv6?

Advantages

```text
Huge Number

of Addresses

↓

Better Security

↓

More Efficient Routing

↓

Supports

Future Growth
```

---

# IPv4 vs IPv6

| IPv4 | IPv6 |
|-------|------|
|32 bits|128 bits|
|4 Bytes|16 Bytes|
|Uses dots|Uses colons|
|Limited Addresses|Huge Address Space|

---

# Public IP Address

Suppose

you open

Google.

Question

Can Google

send data

to

```text
192.168.1.10
```

No.

Because

that address

exists

only

inside

your home network.

Devices

on the Internet

cannot reach it.

Instead,

your ISP

assigns

your Router

a

Public IP Address.

Example

```text
103.25.180.42
```

This address

is visible

on

the Internet.

Anyone

can communicate

with it.

---

# Private IP Address

Inside

your home,

your Router

assigns

addresses like

```text
192.168.x.x

10.x.x.x

172.16.x.x

to

172.31.x.x
```

These are called

Private IP Addresses.

They work

only

inside

your Local Network.

Routers

on the Internet

ignore them.

---

# Public vs Private

Imagine

an apartment.

Public Address

↓

Building Address

used

by

the courier.

Private Address

↓

Flat Number

used

inside

the building.

The courier

never cares

about

your bedroom.

He only needs

the building.

Similarly,

the Internet

uses

the Public IP.

Your Router

handles

the Private IPs.

---

# Static IP

Question

Does

your IP Address

always remain

the same?

Sometimes

Yes.

If

the address

never changes,

it is called

```text
Static IP.
```

Commonly used by

```text
Servers

Companies

Banks
```

Because

clients

must always know

where

the server is.

---

# Dynamic IP

Home Internet

usually uses

Dynamic IPs.

Whenever

you reconnect,

your ISP

may assign

a new

IP Address.

This is done

using

DHCP,

which we'll study

later.

---

# Static vs Dynamic

| Static | Dynamic |
|----------|----------|
|Fixed Address|Changes Automatically|
|Server|Home Users|
|Manual|Automatic|

---

# Logical vs Physical Address

This is

a favourite

interview question.

Remember

```text
MAC Address

↓

Physical Address

↓

NIC

----------------

IP Address

↓

Logical Address

↓

Network Layer
```

---

# Common Misconceptions

### Does every device have an IP Address?

```text
Yes,

if it wants

to communicate

over a network.
```

---

### Does every device have a MAC Address?

```text
Yes.

Every Network Interface Card

has

one.
```

---

### Which one changes?

Usually

```text
IP Address

may change.

MAC Address

normally does not.
```

---

# Interview Questions

### What is an IP Address?

```text
A logical address

used to identify

a device

on a network.
```

---

### Why do we need IP Addresses?

```text
Routers

need

destination addresses

to forward Packets.
```

---

### Difference between

MAC and IP?

```text
MAC

↓

Physical Address

↓

Local Network

----------------

IP

↓

Logical Address

↓

Internet Communication
```

---

### Difference between

IPv4 and IPv6?

```text
IPv4

↓

32 bits

----------------

IPv6

↓

128 bits
```

---

### Public vs Private IP?

```text
Public

↓

Internet

----------------

Private

↓

Local Network
```

---

### Static vs Dynamic IP?

```text
Static

↓

Fixed

----------------

Dynamic

↓

Automatically Assigned
```

---

# Bridge

Now

every device

has

an IP Address.

Question

Suppose

your laptop

has

```text
192.168.1.10
```

and

Google

has

```text
142.250.xxx.xxx
```

How does

your Packet

know

which path

to follow

through

millions

of Routers?

To answer that,

we first need

to understand

how

Networks

are divided

into

smaller networks.

This concept

is called

```text
Subnetting.
```

# 3. Subnetting

So far,

we know

that every device

needs

an IP Address.

Question:

Suppose

a company has

10,000 computers.

Should they all

be placed

inside

one giant network?

```text
No.
```

Why?

Because

everyone

would broadcast

to everyone,

traffic would increase,

management becomes difficult,

and performance decreases.

The solution is

```text
Subnetting.
```

---

## What is Subnetting?

Subnetting means

```text
Dividing

one large network

into

multiple

smaller networks

called

Subnets.
```

Think

of

a university.

Instead of

keeping

every student

inside

one classroom,

we divide them into

```text
CSE

ECE

MECH

CIVIL
```

Management becomes easier.

Networking

uses

the same idea.

---

## Why Do We Need Subnetting?

Without Subnetting

```text
One Huge Network

↓

Too much Broadcast Traffic

↓

Poor Performance

↓

Hard to Manage
```

With Subnetting

```text
Small Networks

↓

Less Broadcast

↓

Better Security

↓

Better Performance

↓

Easy Management
```

---

## Network ID and Host ID

Every IPv4 Address

has

two parts.

```text
Network ID

+

Host ID
```

Example

```text
192.168.1.15
```

Here

```text
192.168.1

↓

Network

---------------

15

↓

Host
```

Meaning

```text
Network ID

↓

Which Network?

----------------

Host ID

↓

Which Device?
```

Think

of

your address.

```text
Apartment Name

↓

Network

--------------

Flat Number

↓

Host
```

---

## CIDR

Earlier,

people used

Classes

(Class A,

B,

C).

Today,

almost everything

uses

```text
CIDR

(Classless Inter-Domain Routing)
```

Instead of saying

```text
Class C
```

we write

```text
192.168.1.0/24
```

The

```text
/24
```

means

```text
First

24 bits

↓

Network

Remaining

8 bits

↓

Hosts
```

For interviews,

you only need

the concept.

No need

to solve

CCNA-style

Subnetting calculations.

---

## Interview Questions

### Why do we need Subnetting?

```text
To divide

one large network

into

smaller networks,

reducing broadcast traffic

and improving

performance

and management.
```

---

### What are

Network ID

and

Host ID?

```text
Network ID

↓

Identifies

the Network.

--------------

Host ID

↓

Identifies

a Device

inside

that Network.
```

---

# 4. Router

We already know

that

Switches

connect devices

inside

one LAN.

Question

How does

your packet

reach

Google?

It passes through

multiple

```text
Routers.
```

---

## What is a Router?

A Router

is a

Network Layer

device.

Its job is

```text
Receive Packet

↓

Read Destination IP

↓

Choose Best Path

↓

Forward Packet
```

Notice

Routers

never care

about

files,

videos,

or messages.

They only care

about

```text
Destination IP Address.
```

---

## Example

Suppose

you open

```text
youtube.com
```

Your Packet

travels

like this.

```text
Laptop

↓

Home Router

↓

ISP Router

↓

Many Internet Routers

↓

Google Router

↓

Google Server
```

Every Router

asks

only one question.

```text
Where

should I send

this Packet

next?
```

---

## Routing Table

Every Router

maintains

a

```text
Routing Table.
```

Example

| Destination Network | Next Hop |
|---------------------|----------|
|192.168.1.0|Local|
|10.0.0.0|Router A|
|0.0.0.0|ISP|

Think

of

Google Maps.

```text
Destination

↓

Best Route

↓

Next Turn
```

A Routing Table

works

the same way.

---

## Default Gateway

Suppose

your laptop

wants

to reach

Google.

Question

Does

your laptop

know

the entire Internet?

```text
No.
```

It knows only

one Router.

That Router

is called

the

```text
Default Gateway.
```

Whenever

your laptop

doesn't know

where to send

a Packet,

it simply sends it

to

the Default Gateway.

Usually,

this is

your

Wi-Fi Router.

---

## Interview Questions

### What does

a Router do?

```text
Routes Packets

between

different Networks

using

IP Addresses.
```

---

### What is

a Routing Table?

```text
A table

used by Routers

to determine

the next hop

for a Packet.
```

---

### What is

the Default Gateway?

```text
The first Router

to which

a device sends

Packets

destined

for another Network.
```

---

# 5. Routing

Routing means

```text
Finding

the path

from

Source

to

Destination.
```

Example

```text
Laptop

↓

Router 1

↓

Router 2

↓

Router 3

↓

Google
```

The Packet

does not

magically know

the route.

Every Router

independently

chooses

the next hop.

---

## Static Routing

Here,

the network administrator

manually configures

the Routing Table.

Advantages

```text
Simple

Predictable
```

Disadvantages

```text
Not scalable

Manual updates
```

Used only

in

small networks.

---

## Dynamic Routing

Large networks

cannot

manually update

thousands

of routes.

Routers

automatically

exchange

routing information

using

routing protocols.

Examples

```text
OSPF

RIP

BGP
```

For SDE interviews,

remember only

```text
Static

↓

Manual

--------------

Dynamic

↓

Automatic
```

No protocol details

are required.

---

## TTL (Time To Live)

Question

Suppose

because of

a routing mistake,

a Packet

keeps moving

in circles.

```text
Router A

↓

Router B

↓

Router C

↓

Router A

↓

...
```

Will it

travel forever?

```text
No.
```

Every Packet

contains

a field called

```text
TTL

(Time To Live).
```

Each Router

reduces

TTL

by

1.

When TTL

becomes

```text
0
```

the Packet

is discarded.

TTL

prevents

infinite routing loops.

---

## Interview Questions

### What is Routing?

```text
The process

of forwarding

Packets

towards

their destination.
```

---

### Static vs Dynamic Routing?

```text
Static

↓

Manual

----------------

Dynamic

↓

Automatic
```

---

### Why is TTL needed?

```text
To prevent

Packets

from looping forever

inside

the network.
```

---

# Bridge

Now,

we know

how

Routers

forward Packets.

Question:

Suppose

your home

has

```text
Phone

Laptop

TV

Tablet
```

All of them

have

Private IP Addresses.

But

your ISP

gave

only

one

Public IP Address.

Question:

How can

all devices

access

the Internet

simultaneously?

This problem

is solved by

```text
NAT

(Network Address Translation).
```

NAT

is one of

the most frequently asked

Computer Networks

interview topics,

and we'll study it next.

# 6. NAT (Network Address Translation)

NAT is one of

the highest ROI

Computer Networks

interview topics.

Question:

Suppose

your home

has

```text
Laptop

Phone

TV

Tablet

Alexa
```

Every device

needs

Internet access.

Question:

Does your ISP

give

every device

its own

Public IP?

```text
No.
```

Usually,

your ISP

gives

only

ONE

Public IP Address.

Example

```text
Public IP

103.45.210.15
```

Now the question is

```text
How can

5 devices

share

one Public IP?
```

The answer is

```text
NAT
```

---

## The Problem

Inside your home,

devices use

Private IPs.

Example

```text
Laptop

192.168.1.2

---------------

Phone

192.168.1.3

---------------

TV

192.168.1.4
```

Google

cannot send

packets directly

to

```text
192.168.x.x
```

because

Private IPs

are not routable

on the Internet.

Someone

must convert

Private IP

↓

Public IP.

That "someone"

is

your Router.

---

## How NAT Works

Suppose

your Laptop

opens

```text
google.com
```

Step 1

Laptop sends

```text
Source IP

192.168.1.2

Destination IP

142.xxx.xxx.xxx
```

to the Router.

---

Step 2

Router changes

the Source IP

to

its own

Public IP.

```text
Before

192.168.1.2

↓

After

103.45.210.15
```

Now

Google thinks

the request came

from

the Router.

---

Step 3

Google replies

to

```text
103.45.210.15
```

Question

How does

the Router know

which device

requested it?

Because

it maintains

a

```text
NAT Table.
```

Example

| Private IP | Port | Public Port |
|------------|------|-------------|
|192.168.1.2|5100|40001|
|192.168.1.3|5101|40002|

Using this table,

the Router

forwards

the reply

to

the correct device.

---

### NAT (Network Address Translation)

## Step 1: Laptop sends a packet

```text
Source IP      = 192.168.1.10
Source Port    = 52341

Destination IP = Google's IP
Destination Port = 443
```

---

## Step 2: Router performs NAT

The router replaces the **private IP** with its **public IP** and may also change the **source port**.

```text
Source IP      = 49.207.100.5
Source Port    = 60001

Destination IP = Google's IP
Destination Port = 443
```

The router stores this mapping in its **NAT Translation Table**.

```text
49.207.100.5:60001
        ↓
192.168.1.10:52341
```

---

## Step 3: Google replies

Google sends the response to the router's **public IP** and **public port**.

```text
Destination IP   = 49.207.100.5
Destination Port = 60001
```

The router looks up the NAT table, finds the corresponding **private IP** and **private port**, changes the packet back, and forwards it to the laptop.


## Why NAT Is Useful

```text
Conserves

Public IP Addresses

↓

Improves Security

↓

Allows

Many Devices

to Share

One Public IP
```

---

## Interview Questions

### Why is NAT needed?

```text
To allow

multiple devices

with Private IPs

to share

one Public IP.
```

---

### Which device performs NAT?

```text
Router.
```

---

### Does NAT change

MAC Address?

```text
No.

It changes

IP Addresses.
```

---

# 7. DHCP (Dynamic Host Configuration Protocol)

Question

Imagine

an office

with

500 computers.

Should

the administrator

manually assign

an IP Address

to every computer?

```text
No.
```

That would be

slow,

error-prone,

and difficult

to maintain.

Instead,

we use

```text
DHCP.
```

---

## What is DHCP?

DHCP automatically assigns

```text
IP Address

↓

Subnet Mask

↓

Default Gateway

↓

DNS Server
```

to a device

when it joins

a network.

---

## DORA Process

DHCP follows

four simple steps.

Remember

```text
DORA
```

### D — Discover

The client

broadcasts

```text
Is there

any DHCP Server?
```

---

### O — Offer

DHCP Server replies

```text
I can give you

192.168.1.25
```

---

### R — Request

Client replies

```text
Yes,

I want

that address.
```

---

### A — Acknowledge

Server confirms

```text
Done.

You may use

192.168.1.25
```

Connection established.

---

## Lease Time

DHCP

does not

permanently assign

an IP Address.

Instead,

it gives

the address

for

a limited time,

called

the

```text
Lease Time.
```

After that,

the client

renews

the lease.

---

## Interview Questions

### What is DHCP?

```text
A protocol

that automatically

assigns

network configuration

to devices.
```

---

### What is DORA?

```text
Discover

↓

Offer

↓

Request

↓

Acknowledge
```

---

# 8. ICMP (Internet Control Message Protocol)

ICMP

is not used

to transfer

application data.

Instead,

it is used

to

report

network information

and

errors.

---

## Ping

When you run

```bash
ping google.com
```

your computer

sends

ICMP Echo Requests.

Google replies

with

ICMP Echo Replies.

If replies arrive,

the destination

is reachable.

---

## Traceroute

Question

Suppose

a packet

passes through

10 Routers.

Can we see

the path?

Yes.

Using

```text
Traceroute.
```

Traceroute

uses

TTL.

Each Router

where TTL expires

sends back

an ICMP message.

This lets us

discover

every Router

between

source

and

destination.

---

## Interview Questions

### What is ICMP?

```text
A protocol

used for

network diagnostics

and

error reporting.
```

---

### Which command

uses ICMP?

```text
Ping.
```

---

### Which command

shows

the path

to a destination?

```text
Traceroute.
```

---

# 9. IPv4 vs IPv6 (Quick Revision)

| IPv4 | IPv6 |
|------|------|
|32 bits|128 bits|
|Uses Dots|Uses Colons|
|Limited Addresses|Huge Address Space|
|Older|Newer|

Remember

IPv6 exists

mainly because

IPv4 addresses

were running out.

---

# 10. Chapter Revision

```text
Network Layer

↓

Uses

IP Address

↓

Router

↓

Routing

↓

Routing Table

↓

Default Gateway

↓

Subnetting

↓

CIDR

↓

TTL

↓

NAT

↓

DHCP

↓

ICMP
```

---

# Interview Rapid Fire

### Which layer uses IP Address?

```text
Network Layer.
```

---

### Which device works at the Network Layer?

```text
Router.
```

---

### Why is Subnetting used?

```text
To divide

a large network

into smaller networks.
```

---

### What is CIDR?

```text
Modern notation

for representing

network prefixes.
```

---

### Why is TTL used?

```text
To prevent

infinite routing loops.
```

---

### Why is NAT needed?

```text
To allow

multiple devices

to share

one Public IP.
```

---

### What does DHCP do?

```text
Automatically assigns

IP configuration

to devices.
```

---

### What is DORA?

```text
Discover

Offer

Request

Acknowledge
```

---

### What does ICMP do?

```text
Reports

network errors

and

is used by

Ping

and

Traceroute.
```

---

# End of CN02

You now understand

how a packet

travels

from your laptop

to another network,

how routers

find paths,

how devices

obtain IP addresses,

how one public IP

serves multiple devices,

and how network connectivity

is diagnosed.

The next chapter,

**CN03 – Transport Layer**,

will answer the next major question:

```text
Once the packet

reaches

the destination,

how do we ensure

the complete message

arrives correctly,

in order,

without loss?

↓

TCP vs UDP
```

# Packet Structure

So far,

we know

that the

Network Layer

moves

Packets.

Question

What exactly

is

inside

a Packet?

A Packet

contains

two parts.

```text
+----------------------+
|      IP Header       |
+----------------------+
|       Payload        |
+----------------------+
```

The

Header

contains

information

needed

to deliver

the Packet.

The

Payload

contains

the actual

data.

---

## Important Header Fields

```text
Source IP

↓

Destination IP

↓

TTL

↓

Protocol

↓

Total Length
```

The Router

mostly reads

the Header.

It does

not care

about

the Payload.

---

## Interview Question

### What is inside an IP Packet?

```text
Header

+

Payload
```

The Header

contains

routing information.

The Payload

contains

the actual data.

# MTU (Maximum Transmission Unit)

Question

Can a network cable

carry

a Packet

of

any size?

```text
No.
```

Every network

has

a maximum

Packet size

called

```text
MTU

(Maximum Transmission Unit)
```

For Ethernet,

the MTU

is usually

```text
1500 Bytes.
```

If

a Packet

is larger,

it must

be divided.

This process

is called

```text
Fragmentation.
```

---

## Interview Question

### What is MTU?

```text
The maximum

Packet size

that can travel

without being

fragmented.
```

# Fragmentation

Suppose

a Packet

has size

```text
4000 Bytes
```

but

the MTU

is

```text
1500 Bytes.
```

Question

Can the Router

send it

directly?

```text
No.
```

The Packet

is divided

into

smaller Packets.

```text
4000 Bytes

↓

1500

↓

1500

↓

1000
```

Each

fragment

travels

independently.

The destination

reassembles

them

to form

the original Packet.

---

## Interview Question

### Why is Fragmentation needed?

```text
Because

a Packet

may be larger

than the MTU.
```

# Default Route

Suppose

a Router

doesn't know

the exact

destination.

Question

What should

it do?

Instead of

dropping

the Packet,

it forwards

it to

the

```text
Default Route.
```

Usually

represented as

```text
0.0.0.0/0
```

Meaning

```text
If no better route exists,

use this route.
```

---

## Interview Question

### What is the Default Route?

```text
The route used

when

no specific route

matches

the destination.
```

# Longest Prefix Match

Suppose

a Router

has

these routes.

```text
192.168.0.0/16

192.168.1.0/24
```

Packet

destination

```text
192.168.1.45
```

Question

Which route

should

the Router

choose?

Answer

```text
192.168.1.0/24
```

because

it is

more specific.

This rule

is called

```text
Longest Prefix Match.
```

Routers

always prefer

the most

specific route.

---

## Interview Question

### What is Longest Prefix Match?

```text
The Router

chooses

the most specific

matching route.
```

# Loopback Address

Sometimes

a computer

needs

to communicate

with

itself.

Question

Does it

send data

onto

the Internet?

```text
No.
```

Instead,

it uses

the

Loopback Address.

```text
127.0.0.1
```

This always

refers

to

the same

computer.

It is commonly

called

```text
localhost
```

---

## Interview Question

### What is 127.0.0.1?

```text
The Loopback Address.

Used

to communicate

with the same machine.
```

# Broadcast Address

Sometimes

we want

to send

a Packet

to

every device

inside

a network.

Instead of

sending

many Packets,

we use

a

```text
Broadcast Address.
```

Example

```text
255.255.255.255
```

Every device

inside

the Local Network

receives

the Packet.

Routers

normally

do not forward

Broadcast Packets.

---

## Interview Question

### What is a Broadcast Address?

```text
An address

used to send

a Packet

to every device

inside

a Local Network.
```

# Reserved Private IP Ranges

Private IP Addresses

cannot

be used

on

the Internet.

They are

reserved

for

Local Networks.

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```

These ranges

are used

inside

homes,

schools,

and companies.

Routers

on the Internet

do not route

these addresses.

---

## Interview Question

### Name the three Private IP ranges.

```text
10.0.0.0/8

172.16.0.0/12

192.168.0.0/16
```
