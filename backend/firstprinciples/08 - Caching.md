# LEC 08 - Caching (Backend Essentials)

# What is Caching?

Caching stores **frequently used data** in a **faster storage** so future requests can be served quickly.

Goal

- Reduce response time
- Reduce server load
- Avoid repeating expensive work

---

# Why do we need Caching?

Without caching

```text
Client

↓

Server

↓

Database / Expensive Computation

↓

Response
```

Every request repeats the same work.

With caching

```text
Client

↓

Server

↓

Cache

↓

Response
```

Only cache misses go to the database.

---

# Cache Hit vs Cache Miss

## Cache Hit

Requested data already exists.

```text
Client

↓

Cache

↓

Response
```

Fast.

---

## Cache Miss

Data not present.

```text
Client

↓

Cache

↓

Database

↓

Store in Cache

↓

Response
```

Slower only once.

---

# Benefits

- Lower latency
- Faster APIs
- Reduced database load
- Lower infrastructure cost
- Better scalability

---

# When Should You Cache?

Cache when

- Data is read frequently
- Data changes rarely
- Computation is expensive
- External API calls are expensive

Avoid caching

- Frequently changing data
- Sensitive data unless necessary
- Very small or rarely accessed data

---

# Levels of Caching

## 1. Network Cache

Examples

- CDN
- DNS Cache

---

## 2. Hardware Cache

Examples

- CPU Cache (L1, L2, L3)
- RAM

Mostly handled by hardware.

Backend engineers rarely interact with it directly.

---

## 3. Application Cache

Most important for backend engineers.

Examples

- Redis
- Memcached

---

# CDN (Content Delivery Network)

CDN stores static content closer to users.

Example

```text
Images

CSS

JavaScript

Videos
```

Instead of

```text
India

↓

USA Server
```

Use

```text
India

↓

Nearest CDN Server
```

Benefits

- Lower latency
- Faster page loads
- Lower origin server load

---

# CDN Workflow

```text
User

↓

Nearest CDN Server

↓

Cache Hit?

↓

Yes → Return Content

↓

No

↓

Origin Server

↓

Store in CDN

↓

Return Content
```

---

# DNS Caching

DNS converts

```text
google.com

↓

IP Address
```

DNS responses are cached to avoid repeated lookups.

Caching exists at multiple levels

- Browser
- Operating System
- ISP Resolver

---

# RAM vs Disk

Cache uses RAM.

Database uses Disk.

| RAM | Disk |
|------|------|
| Very Fast | Slower |
| Expensive | Cheap |
| Volatile | Persistent |

This is why Redis is much faster than PostgreSQL.

---

# Redis

Redis is an

- In-memory
- Key-Value
- NoSQL

database.

Example

```text
user:101

↓

User Object
```

Redis is mainly used as a cache, not a primary database.

---

# Cache Strategies

## 1. Cache Aside (Lazy Loading)

Most common strategy.

```text
Request

↓

Cache

↓

Miss

↓

Database

↓

Store in Cache

↓

Response
```

Advantages

- Easy
- Memory efficient

---

## 2. Write Through

Whenever data changes

```text
Database

+

Cache
```

are updated together.

Advantages

- Cache always stays fresh.

Disadvantages

- Slower writes.

---

# Cache Eviction

Cache memory is limited.

Old data must eventually be removed.

---

## LRU (Least Recently Used)

Removes the item that hasn't been accessed recently.

Most commonly used.

---

## LFU (Least Frequently Used)

Removes the item accessed the fewest times.

Useful for stable access patterns.

---

## TTL (Time To Live)

Every cached item gets an expiry time.

Example

```text
Weather Data

↓

Expire after 1 hour
```

After expiry,

next request fetches fresh data.

---

# Common Redis Use Cases

## Database Query Caching

Expensive SQL query

↓

Redis

↓

Future requests served from cache.

---

## Session Storage

Store

- Login sessions
- Authentication sessions

Fast lookup.

---

## External API Caching

Instead of calling

```text
Weather API
```

every request,

cache the response.

Benefits

- Lower cost
- Avoid rate limits
- Faster response

---

## Rate Limiting

Redis tracks

```text
IP Address

↓

Number of Requests
```

Example

```text
100 requests

per minute
```

Very common interview question.

---

# When NOT to Cache

Don't cache

- Frequently changing data
- Financial transactions
- Data requiring strong consistency
- One-time operations

---

# Interview Takeaways

- Cache stores frequently used data in fast storage.
- Cache Hit = data found.
- Cache Miss = fetch from source and store.
- Redis is an in-memory key-value NoSQL database.
- Cache Aside is the most common caching strategy.
- Write Through keeps cache synchronized with the database.
- LRU, LFU and TTL are common cache eviction strategies.
- Common Redis use cases:
  - Database query caching
  - Session storage
  - External API caching
  - Rate limiting
- Cache improves read performance but introduces cache consistency challenges.


# LEC 08 - Caching (Part 2)

# Rate Limiting

Rate limiting controls how many requests a client can make within a specific time.

Purpose

- Prevent abuse
- Prevent DDoS attacks
- Protect APIs
- Ensure fair resource usage

Example

```text
100 requests

per minute

per IP
```

If the limit is exceeded,

return

```text
HTTP 429

Too Many Requests
```

---

# Why Redis for Rate Limiting?

Every request needs to

- Check request count
- Increment request count
- Check expiry

These operations happen extremely frequently.

Using a database would create unnecessary load.

Redis is ideal because

- Very fast
- In-memory
- Supports automatic key expiration (TTL)
- Atomic operations

---

# Simple Rate Limiting Flow

```text
Client

↓

API

↓

Redis

↓

Request Count < Limit ?

↓

Yes

↓

Allow Request

↓

Increment Counter

↓

Return Response
```

Otherwise

```text
Client

↓

API

↓

Redis

↓

Limit Exceeded

↓

HTTP 429
```

---

# Common Rate Limiting Algorithms

## Fixed Window

Example

```text
100 requests

per minute
```

Counter resets every minute.

Pros

- Very simple

Cons

- Can allow bursts near window boundaries.

---

## Sliding Window

Instead of fixed intervals,

uses a moving time window.

Advantages

- Fairer
- Smoother traffic

Common in production systems.

---

## Token Bucket

Requests consume tokens.

Tokens refill over time.

Allows occasional bursts while maintaining an average request rate.

Very commonly used.

---

## Leaky Bucket

Requests enter a queue.

They leave at a constant rate.

Useful for smoothing sudden traffic spikes.

---

# Choosing an Algorithm

| Algorithm | Best For |
|-----------|----------|
| Fixed Window | Simple APIs |
| Sliding Window | Fair rate limiting |
| Token Bucket | APIs allowing short bursts |
| Leaky Bucket | Traffic smoothing |

---

# Redis Data Structure Example

Redis stores

```text
Key

↓

Value
```

Example

```text
ip:192.168.1.10

↓

57
```

Meaning

```text
This IP

has made

57 requests.
```

TTL automatically removes old counters.

---

# Why Cache Instead of Database?

Database

```text
Slower

Persistent

Expensive Reads
```

Redis

```text
Very Fast

Memory Based

Perfect for Temporary Data
```

Temporary data examples

- Sessions
- Request counters
- OTPs
- Cached API responses
- Frequently accessed query results

---

# General Rule for Caching

Ask yourself

> "If this data disappears, can my application regenerate it?"

If

**Yes**

→ Good candidate for caching.

If

**No**

→ Store it permanently in the database.

Example

| Data | Cache? |
|------|--------|
| Product Details | ✅ Yes |
| Weather API Response | ✅ Yes |
| User Session | ✅ Yes |
| Dashboard Statistics | ✅ Yes |
| User Password | ❌ No |
| Orders | ❌ No |
| Bank Transactions | ❌ No |

---

# Backend Interview Tips

You should know

- What caching is
- Cache Hit vs Cache Miss
- Redis basics
- Cache Aside
- Write Through
- LRU, LFU, TTL
- Why Redis is fast
- Why Redis is used for sessions
- Why Redis is used for rate limiting
- CDN basics
- Cache consistency trade-offs

---

# Final Mental Model

```text
Request

↓

Cache

↓

Hit?

↓

Yes

↓

Return Immediately

↓

No

↓

Database / External API

↓

Store in Cache

↓

Return Response
```

The goal of caching is simple:

- Avoid repeated expensive work.
- Reduce latency.
- Reduce database load.
- Scale applications efficiently.
