# LEC 16 - Scaling & Performance

- [16A - Performance Fundamentals](#lec-16a---scaling--performance-part-1---performance-fundamentals)
  - What is Performance?
  - Latency
  - Average vs Percentiles (P50, P90, P95, P99)
  - Throughput
  - Utilization & Headroom
  - Bottlenecks
  - Profiling & Flame Graphs
  - CPU-bound vs IO-bound
  - Distributed Tracing

- [16B - Database Performance](#lec-16b---scaling--performance-part-2---database-performance)
  - Database Bottlenecks
  - N+1 Query Problem
  - JOINs & Bulk Fetch
  - Indexes
  - Full Table Scan
  - Pagination
  - Query Optimization

- [16C - Caching](#lec-16c---scaling--performance-part-3---caching)
  - Why Caching?
  - Cache Hit / Cache Miss
  - Cache Invalidation
  - TTL
  - Write Through
  - Cache Aside
  - Cache Consistency
  - Cache Stampede
  - Cache Eviction
  - Best Practices

- [16D - Scaling Strategies](#lec-16d---scaling--performance-part-4---scaling-strategies)
  - Vertical Scaling
  - Horizontal Scaling
  - Stateless vs Stateful
  - Shared State
  - Load Balancer
  - Health Checks
  - Read Replicas
  - Queues
  - Scaling Best Practices

- [16E - Statelessness & Load Balancers](#lec-16e---scaling--performance-part-5---statelessness--load-balancers)
  - Statelessness
  - Shared Storage
  - Sessions
  - Object Storage
  - Centralized Databases
  - Round Robin
  - Weighted Round Robin
  - Least Connections
  - Active Connections
  - Health Checks
  - Horizontal Scaling Checklist

- [16F - Database Scaling](#lec-16f---scaling--performance-part-6---database-scaling)
  - Read Replicas
  - Replication Lag
  - Eventual Consistency
  - Sharding
  - Shard Key
  - Managed Databases
  - Read Replicas vs Sharding

- [16G - Content Delivery Networks (CDNs)](#lec-16g---scaling--performance-part-7---content-delivery-networks-cdns)
  - Why CDNs?
  - Origin Server
  - Edge Servers
  - Cache Hit / Cache Miss
  - Static vs Dynamic Content
  - CDN vs Redis
  - CDN Request Flow

- [16H - Final CDN Concepts & Production Mindset](#lec-16h---scaling--performance-part-8---final-cdn-concepts--production-mindset)
  - Edge Caching
  - Multi-Level Caching
  - Performance Strategy
  - Production Trade-offs
  - Complete Backend Architecture
  - Backend Performance Workflow
  - Interview Revision
  - Final Golden Rules

# LEC 16A - Scaling & Performance (Part 1) - Performance Fundamentals

# What is Performance?

A backend is considered **fast** when it responds quickly to user requests.

A typical request flow is

```text
User

↓

Browser

↓

Internet

↓

Backend Server

↓

Database / External APIs

↓

Backend Response

↓

Browser Rendering
```

The total time from

- User action
- Backend processing
- Browser rendering

is what the user experiences as performance.

---

# Latency

## What is Latency?

Latency is

> **The total time taken for one request to complete.**

Example

```text
Click Button

↓

Request Sent

↓

Backend Processes

↓

Database Query

↓

Response Returned

↓

UI Updates
```

Everything contributes to latency

- Network delay
- Backend processing
- Database queries
- External APIs
- Browser rendering

When users say

> "The app feels slow."

they are almost always talking about **latency**.

---

# Latency is NOT Constant

Different requests take different amounts of time.

Example

```text
Request A

50 ms

(Cache Hit)

↓

Fast
```

```text
Request B

200 ms

(Database Query)

↓

Slower
```

Reasons

- Cache hit vs cache miss
- Server load
- Network conditions
- Database performance
- External API delays

Real systems are unpredictable.

---

# Why Average Latency is Misleading

Suppose

```text
99% Requests

50 ms
```

```text
1% Requests

5 seconds
```

Average latency may still look good,

but some users have an awful experience.

Those users matter.

Averages hide outliers.

---

# Percentiles

Instead of averages,

backend engineers measure latency using **percentiles**.

---

## P50

50% of requests finish within this time.

Represents the "typical" user.

---

## P90

90% of requests finish within this time.

The slowest 10% are slower than this.

---

## P95

95% of requests finish within this time.

Very common production metric.

---

## P99 ⭐

99% of requests finish within this time.

Only the slowest 1% are slower.

This is one of the most important production metrics.

---

# Why P95 & P99 Matter

The slowest requests usually involve

- Complex business logic
- Large database queries
- Payment workflows
- External APIs
- Heavy synchronization

These requests often belong to your most valuable users.

Optimizing P99 usually improves real user experience more than improving averages.

---

# Throughput

Latency answers

```text
How long does one request take?
```

Throughput answers

```text
How many requests can the system handle?
```

Common units

- Requests/second (RPS)
- Requests/minute

---

# Latency vs Throughput

Example

```text
10 Requests/sec

↓

150 ms latency
```

Later

```text
1000 Requests/sec

↓

2 second latency
```

The same application became slower simply because more work arrived.

A system must be evaluated using **both** latency and throughput.

---

# Questions Throughput Helps Answer

- Can we survive Black Friday traffic?
- Can we handle a marketing campaign?
- How many concurrent users are supported?
- When do we need more servers?

---

# Utilization

Utilization is

> **How much of the system's capacity is currently being used.**

Examples

```text
0%

Idle
```

```text
50%

Comfortable
```

```text
80%

Busy
```

```text
100%

Fully Utilized
```

---

# Ice Cream Shop Analogy

Imagine one employee serving customers.

Few customers

```text
No Queue

↓

Immediate Service

↓

Low Latency
```

Many customers

```text
Queue Forms

↓

Everyone Waits

↓

Higher Latency
```

The employee isn't slower.

People simply wait longer.

Servers behave the same way.

---

# Utilization vs Latency

Many people expect

```text
Higher Utilization

↓

Slightly Higher Latency
```

Reality

```text
Low Utilization

↓

Small Latency Increase

↓

Around 70–80%

↓

Latency Starts Rising Quickly

↓

Near 100%

↓

Latency Explodes
```

This happens because requests begin waiting in queues.

---

# Highway Analogy

Imagine a highway.

### 50% Capacity

Cars move freely.

Traffic is predictable.

---

### 80% Capacity

Traffic slows slightly.

Lane changes become harder.

---

### 90% Capacity

Traffic becomes unpredictable.

Small disturbances create traffic jams.

---

### 100% Capacity

Everything stops.

The road is full.

---

Backend servers behave similarly.

---

# Never Run at 100% Utilization

Production systems intentionally keep spare capacity.

Typical target

```text
60–80% Utilization
```

Remaining capacity acts as a safety buffer.

Reasons

- Traffic spikes
- Bursty workloads
- Unexpected load
- Failures

This spare capacity is called **headroom**.

---

# Traffic is Bursty

Real traffic is not steady.

Instead of

```text
100

100

100

100
```

It looks more like

```text
50

800

120

1400

75

300
```

Systems need headroom to absorb these bursts.

---

# Bottlenecks

Whenever a system is slow,

something specific is causing the delay.

That limiting component is called the

> **Bottleneck**

Possible bottlenecks

- Database
- Network
- CPU
- Disk
- External API
- Serialization
- Logging
- Cache

---

# Never Guess the Bottleneck

Many engineers immediately assume

- Database is slow
- Need caching
- Need more servers

These assumptions are often wrong.

Always measure first.

---

# Example

Suppose

```text
GET /products
```

feels slow.

You add

```text
Redis Cache
```

After deployment

```text
Still Slow
```

You profile the request.

Results

```text
Database

10 ms
```

```text
Cache

5 ms
```

```text
Synchronous Logging

500 ms
```

The real bottleneck wasn't the database.

It was logging.

---

# Golden Rule

> **Never optimize assumptions. Optimize measurements.**

---

# Profiling

Profiling measures

> **Where your application spends CPU time.**

It records

- Function execution
- CPU usage
- Execution time
- Call hierarchy

Useful for CPU-intensive workloads.

---

# Flame Graphs

Profilers often display results as

**Flame Graphs**

Characteristics

- Wider blocks = more execution time
- Stacked blocks = function calls
- Quickly highlights expensive functions

---

# CPU-bound vs IO-bound

## CPU-bound

Most time spent computing.

Examples

- Image processing
- Compression
- AI inference
- Encryption

Profilers work very well here.

---

## IO-bound

Most time spent waiting.

Examples

- Database
- Network
- APIs
- Files
- Redis

Typical backend applications are mostly IO-bound.

---

# Distributed Tracing

Profilers show CPU usage.

Distributed Tracing shows

> **Where an individual request spends its time across services.**

Example

```text
Request

↓

API

↓

Database

↓

Redis

↓

Payment Service

↓

Response
```

Each step records its duration.

This makes slow components easy to identify.

---

# Mental Model

```text
Slow Request

↓

Measure

↓

Find Bottleneck

↓

Optimize

↓

Measure Again
```

Never reverse this order.

---

# Best Practices

- Measure before optimizing.
- Ignore average latency.
- Monitor P95 and P99.
- Track throughput with latency.
- Keep utilization below maximum.
- Maintain headroom.
- Profile CPU-heavy workloads.
- Use distributed tracing for request analysis.
- Optimize actual bottlenecks, not assumptions.

---

# Interview Takeaways

Know

- Latency
- Throughput
- Utilization
- Headroom
- P50
- P90
- P95
- P99
- Bottlenecks
- Profiling
- Flame Graph
- Distributed Tracing
- CPU-bound vs IO-bound

---

# Mental Model

```text
Request

↓

Latency

↓

Measure

↓

Find Bottleneck

↓

Optimize

↓

Higher Throughput

↓

Better User Experience
```

**Golden Rule**

> **Performance optimization begins with measurement, not assumptions.**

# LEC 16B - Scaling & Performance (Part 2) - Database Performance

# Why Databases Become Bottlenecks

Databases perform expensive work.

They must

- Store data durably
- Execute queries
- Handle concurrent reads and writes
- Maintain consistency
- Manage transactions
- Recover from failures

Because almost every backend request interacts with the database,

it is one of the most common performance bottlenecks.

---

# Before Optimizing the Database

Do **not** assume

```text
Slow API

↓

Database is Slow
```

Always verify using

- Logging
- Metrics
- Profiling
- Distributed tracing

Only optimize after confirming the database is actually responsible.

---

# Common Database Performance Problems

- N+1 Query Problem
- Missing Indexes
- Full Table Scans
- Large Result Sets
- Unnecessary Queries
- Poor Query Design

---

# N+1 Query Problem

## What is it?

Instead of fetching related data together,

the application performs

```text
1 Query

+

N Additional Queries
```

Example

```text
Fetch 20 Blog Posts

↓

For Each Post

↓

Fetch Author

↓

21 Database Queries
```

---

# Why is it Bad?

Every database query has overhead.

Each query involves

- Network communication
- Query parsing
- Query planning
- Query execution
- Returning results

Even if one query is very fast,

hundreds of queries become expensive.

---

# Growth Problem

Example

```text
20 Posts

↓

21 Queries
```

```text
100 Posts

↓

101 Queries
```

```text
1000 Posts

↓

1001 Queries
```

Performance degrades as data grows.

---

# Solution

Fetch related data in bulk.

Instead of

```text
Loop

↓

Query Database
```

Use

```text
Single Bulk Query

↓

All Required Data
```

Usually achieved using

- JOINs
- Bulk Fetch
- Eager Loading

---

# ORM Support

Most ORMs already provide mechanisms to avoid N+1.

Examples

- Eager Loading
- Include
- Prefetch
- Select Related
- JOIN Fetch

The exact API differs,

but the idea is identical.

---

# Rule

Never execute database queries inside loops unless absolutely necessary.

---

# Indexes

## What is an Index?

An index is a data structure that helps the database locate rows quickly.

Without an index,

the database may have to inspect every row.

---

# Library Analogy

Without an index

```text
Need Book

↓

Search Entire Library

↓

Very Slow
```

With an index

```text
Need Book

↓

Catalog

↓

Direct Shelf

↓

Fast
```

A database index works like the library catalog.

---

# Full Table Scan

Without an index,

the database performs

```text
Row 1

↓

Row 2

↓

Row 3

↓

...

↓

Target Row
```

This is called

- Sequential Scan
- Full Table Scan

It becomes expensive on large tables.

---

# Indexed Search

With an index

```text
Index

↓

Locate Matching Rows

↓

Read Only Those Rows
```

Much less work.

---

# Benefits of Indexes

Indexes improve

- Search
- Filtering
- Sorting
- Joins

Common indexed columns

- Primary Keys
- Foreign Keys
- Frequently Filtered Columns
- Frequently Sorted Columns

---

# Indexes Are Not Free

Indexes improve reads,

but increase write cost.

Every INSERT,

UPDATE,

or DELETE

may also need to update indexes.

Indexes also consume additional storage.

---

# Don't Index Everything

Too many indexes cause

- Slower writes
- Higher storage
- Extra maintenance

Create indexes only for frequently queried columns.

---

# Choose Indexes Based on Access Patterns

Instead of asking

```text
Which columns exist?
```

Ask

```text
Which columns are searched frequently?
```

Indexes should reflect

how the application accesses data.

---

# Large Result Sets

Sometimes the query is fast,

but the response is huge.

Example

```text
SELECT *

↓

100,000 Rows
```

Even if execution is quick,

transferring massive data increases latency.

---

# Fetch Only What You Need

Instead of

```sql
SELECT *
```

Prefer

```text
Only Required Columns
```

Smaller responses mean

- Less network traffic
- Less memory
- Faster serialization

---

# Pagination

Never load an entire dataset.

Bad

```text
Load 1 Million Rows
```

Good

```text
Page 1

↓

20 Rows
```

Pagination reduces

- Database load
- Memory usage
- Network transfer

---

# Query Optimization

Fast systems usually

- Query fewer rows
- Return less data
- Avoid unnecessary work

Optimization is often about

doing **less work**, not faster work.

---

# Measure Before Optimizing

Use tools such as

- Query timing
- Execution plans
- Distributed tracing
- Database metrics

Never optimize blindly.

---

# Typical Optimization Order

```text
Measure

↓

Identify Slow Query

↓

Optimize Query

↓

Add Index

↓

Measure Again
```

Avoid reversing the process.

---

# Common Mistakes

❌ Query inside loops

❌ Missing indexes

❌ Indexing every column

❌ Fetching unnecessary columns

❌ Returning huge datasets

❌ Assuming the database is always slow

---

# Best Practices

- Measure database performance first.
- Avoid N+1 queries.
- Fetch related data in bulk.
- Use JOINs when appropriate.
- Create indexes for frequently queried columns.
- Avoid unnecessary indexes.
- Return only required fields.
- Paginate large datasets.
- Measure after every optimization.

---

# Interview Takeaways

Know

- N+1 Query Problem
- Bulk Fetch
- JOIN
- Eager Loading
- Indexes
- Full Table Scan
- Sequential Scan
- Pagination
- Query Optimization
- Read vs Write Tradeoff of Indexes

---

# Mental Model

```text
Slow Query

↓

Measure

↓

Find Cause

↓

Optimize Query

↓

Add Index (if needed)

↓

Measure Again
```

**Golden Rule**

> **The fastest database query is the one you never execute. When you must query, execute as few queries as possible and read only the data you actually need.**


# LEC 16C - Scaling & Performance (Part 3) - Caching

# Why Caching Exists

Computers are built as a hierarchy of storage.

```text
CPU Cache

↓

RAM

↓

Redis / Memcached

↓

Database

↓

Disk
```

The closer the data is,

the faster it can be accessed.

Caching takes advantage of this idea.

---

# What is Caching?

Caching means

> **Storing frequently accessed data in a faster storage layer to avoid repeating expensive work.**

Instead of

```text
Request

↓

Database

↓

Response
```

we do

```text
Request

↓

Cache

↓

Cache Hit

↓

Response
```

Only when the data is missing do we query the database.

---

# Cache Hit

Requested data already exists in the cache.

```text
Request

↓

Cache

↓

Found

↓

Return Immediately
```

Benefits

- Lower latency
- Lower database load
- Higher throughput

---

# Cache Miss

Requested data is not in cache.

```text
Request

↓

Cache

↓

Not Found

↓

Database

↓

Store in Cache

↓

Return Response
```

Future requests become faster.

---

# Why Caching Helps

Without caching

```text
1000 Requests

↓

1000 Database Queries
```

With caching

```text
1000 Requests

↓

950 Cache Hits

↓

50 Database Queries
```

The database performs much less work.

---

# What Should Be Cached?

Good candidates

- Frequently read data
- Rarely changing data
- Expensive computations
- Popular API responses
- Product information
- User profiles
- Configuration

Poor candidates

- Highly dynamic data
- Frequently updated values
- Sensitive temporary information

---

# Cache Invalidation

The hardest part of caching is deciding

> **When should cached data be removed or refreshed?**

If stale data remains,

users receive outdated information.

---

# Common Cache Invalidation Strategies

## Time-To-Live (TTL)

Each cache entry automatically expires after a fixed duration.

```text
Store

↓

5 Minutes

↓

Automatically Remove
```

Simple and widely used.

---

## Write Through

Whenever the database is updated,

the cache is updated immediately.

```text
Update Database

↓

Update Cache
```

Keeps cache synchronized.

---

## Cache Aside (Lazy Loading)

Most common strategy.

```text
Read

↓

Cache Miss

↓

Database

↓

Store in Cache

↓

Return Response
```

On updates

```text
Update Database

↓

Delete Cache
```

The next read rebuilds the cache.

---

# Cache Consistency

Remember

The database is usually the

**Source of Truth**

The cache is only a temporary copy.

Never treat cache as the permanent data store.

---

# Cache Stampede

Suppose

```text
Popular Item

↓

Cache Expires

↓

10,000 Requests Arrive
```

Every request now queries the database.

The database suddenly becomes overloaded.

This is called a **Cache Stampede**.

---

# Preventing Cache Stampede

Common approaches

- Refresh cache before expiry
- Randomized TTLs
- Request coalescing
- Distributed locks

Goal

Only one request should rebuild the cache.

---

# Cache Eviction

Caches have limited memory.

Eventually,

old entries must be removed.

Common policies

- LRU (Least Recently Used)
- LFU (Least Frequently Used)
- FIFO (First In First Out)

---

# Cache Trade-offs

Advantages

- Faster responses
- Lower database load
- Better scalability

Costs

- Extra memory
- Cache invalidation complexity
- Possible stale data

Caching improves performance,

but increases system complexity.

---

# When NOT to Cache

Avoid caching

- Frequently changing values
- One-time requests
- Highly personalized data (unless designed carefully)
- Security-sensitive temporary information

Sometimes direct database access is simpler and safer.

---

# Measure Before Adding Cache

Do **not** assume

```text
Slow API

↓

Need Cache
```

Maybe the real problem is

- Missing index
- Slow network
- External API
- Serialization
- Logging

Always identify the bottleneck first.

---

# Caching is an Optimization

A correct system should work

without caching.

Caching should only make it

faster.

Never depend on cache for correctness.

---

# Best Practices

- Cache expensive reads.
- Keep the database as the source of truth.
- Choose an appropriate invalidation strategy.
- Use TTL where possible.
- Prevent cache stampedes.
- Monitor cache hit ratio.
- Measure performance before and after caching.

---

# Interview Takeaways

Know

- Cache Hit
- Cache Miss
- Cache Aside
- Write Through
- TTL
- Cache Invalidation
- Cache Stampede
- Cache Eviction
- Source of Truth
- LRU / LFU

---

# Mental Model

```text
Request

↓

Cache

├── Hit

│     ↓

│   Response

│

└── Miss

      ↓

   Database

      ↓

 Store in Cache

      ↓

   Response
```

**Golden Rule**

> **Caching should reduce repeated work, not hide bad system design. Measure first, cache second.**


# LEC 16D - Scaling & Performance (Part 4) - Scaling Strategies

# Why Scaling is Needed

Every system has a limit.

As traffic grows,

eventually one machine cannot handle all requests.

Scaling is the process of increasing system capacity.

---

# Two Ways to Scale

```text
Scaling

├── Vertical Scaling

└── Horizontal Scaling
```

---

# Vertical Scaling (Scale Up)

Increase the power of a single machine.

Examples

- More CPU
- More RAM
- Faster SSD
- Better Network

```text
Old Server

↓

4 CPU
8 GB RAM

↓

Upgrade

↓

16 CPU
64 GB RAM
```

---

# Advantages

- Very simple
- No architecture changes
- No distributed system complexity
- Existing application usually works unchanged

---

# Limitations

Every machine has a maximum size.

Eventually you cannot upgrade further.

Also

- Expensive
- Single point of failure
- Limited scalability

---

# Horizontal Scaling (Scale Out)

Instead of one powerful server,

add more servers.

```text
Before

Client

↓

Server
```

```text
After

        Load Balancer

      /      |      \

 Server1 Server2 Server3
```

Requests are distributed across multiple machines.

---

# Advantages

- Almost unlimited growth
- Better fault tolerance
- High availability
- Easier maintenance

---

# Challenges

Multiple servers introduce new problems

- Load balancing
- Distributed state
- Synchronization
- Consistency
- Monitoring

Horizontal scaling is more powerful,

but also more complex.

---

# Stateless Services

Horizontal scaling works best when servers are stateless.

A stateless server stores

no user-specific session information in memory.

Every request contains everything needed.

Example

```text
Client

↓

JWT Token

↓

Any Server Can Handle Request
```

---

# Stateful Services

A stateful server keeps user information locally.

Example

```text
User

↓

Server A

↓

Session Stored in Memory
```

If the next request reaches

```text
Server B
```

the session is missing.

This creates scaling problems.

---

# Solutions for Shared State

Instead of storing state inside servers,

store it in shared systems.

Examples

- Redis
- Database
- Shared Session Store

Then

every server can access the same data.

---

# Load Balancer

A load balancer sits in front of backend servers.

```text
Clients

↓

Load Balancer

↓

Multiple Servers
```

Responsibilities

- Distribute traffic
- Detect failed servers
- Improve availability
- Prevent overload

---

# Load Balancing Algorithms

Common strategies

### Round Robin

```text
1 → Server A

2 → Server B

3 → Server C

4 → Server A
```

Simple and common.

---

### Least Connections

Send requests to

the server currently handling

the fewest active connections.

Useful when requests have different durations.

---

### Weighted Load Balancing

More powerful servers receive

more requests.

Example

```text
Large Server

↓

70%
```

```text
Small Server

↓

30%
```

---

# Health Checks

A load balancer continuously checks

whether servers are healthy.

If one server fails

```text
Server Offline

↓

Stop Sending Traffic
```

Users continue using healthy servers.

---

# Scaling Databases

Application servers scale more easily

than databases.

Databases are harder because

they store shared state.

---

# Read Replicas

Separate read traffic from write traffic.

```text
          Primary

             |

     ----------------

     |              |

 Replica 1     Replica 2
```

Writes

↓

Primary

Reads

↓

Replicas

This greatly increases read capacity.

---

# Write Bottleneck

Writes usually must reach

the primary database.

Therefore

writes are generally harder to scale

than reads.

---

# Caching Before Scaling

Sometimes

adding servers is unnecessary.

Instead

remove unnecessary database work.

Example

```text
Database

↓

Redis Cache

↓

Fewer Queries

↓

Higher Capacity
```

Scaling is not always about adding hardware.

Sometimes

optimization is enough.

---

# Queues Reduce Load

Expensive tasks should not block requests.

Examples

- Emails
- Notifications
- Image Processing
- Reports

Instead

```text
Request

↓

Queue

↓

Background Worker
```

Users receive faster responses.

---

# Bottlenecks Move

Suppose you optimize

the database.

Now

the network becomes slow.

You optimize networking.

Now

the external API becomes slow.

Optimization shifts bottlenecks.

Performance engineering is continuous.

---

# Measure Before Scaling

Never assume

```text
High Traffic

↓

Need More Servers
```

First identify

- CPU
- Memory
- Database
- Network
- External APIs
- Disk
- Locks
- Serialization

Scale only after finding the real limitation.

---

# Cost vs Performance

More servers

mean

- Higher cloud cost
- More monitoring
- More deployments
- More maintenance

The goal is

not maximum scaling,

but

**enough scaling for expected traffic.**

---

# Best Practices

- Scale only after measuring.
- Prefer optimization before adding hardware.
- Keep backend services stateless.
- Store shared state externally.
- Use load balancers.
- Monitor utilization continuously.
- Scale databases carefully.
- Cache expensive reads.
- Offload heavy work to background jobs.

---

# Interview Takeaways

Know

- Vertical Scaling
- Horizontal Scaling
- Stateless Services
- Stateful Services
- Load Balancer
- Round Robin
- Least Connections
- Read Replicas
- Primary Database
- Health Checks
- Shared Session Store

---

# Mental Model

```text
More Users

↓

Measure Bottleneck

↓

Optimize

↓

If Needed

↓

Scale Up

or

Scale Out

↓

Load Balancer

↓

Multiple Servers
```

**Golden Rule**

> **Scaling should solve the current bottleneck—not simply add more machines. Optimize first, then scale where measurements prove it is necessary.**

# LEC 16E - Scaling & Performance (Part 5) - Statelessness & Load Balancers

# Statelessness

## What is Statelessness?

A backend server is **stateless** if it **does not store user-specific data inside its own memory**.

Any request can be handled by **any server**.

```text
Client

↓

Load Balancer

↓

Server A
Server B
Server C

(All behave identically)
```

---

# Why is Statelessness Required?

Horizontal scaling works only if

```text
Any Request

↓

Any Server

↓

Same Result
```

If one server stores data that others don't know about,

requests start failing.

---

# Stateful vs Stateless

## Stateful

```text
User Login

↓

Server A

↓

Stores Session In RAM
```

Next request

```text
↓

Server B

↓

Session Missing

↓

401 Unauthorized
```

Problem:

Each server has different information.

---

## Stateless

```text
User Login

↓

Shared Storage (Redis)

↓

Server A
Server B
Server C

(All can verify session)
```

Every server sees the same data.

---

# Rule of Horizontal Scaling

Never store important data inside

- Server memory
- Local files
- Local database

Instead store it in shared infrastructure.

---

# Common Shared Storage

## Sessions

Use

- Redis
- Shared Session Store

Never

```text
Server Memory
```

---

## Files

Never save uploads to

```text
Server Disk
```

Instead use

- Amazon S3
- Cloudflare R2
- Blob Storage

Every server can access them.

---

## Database

Avoid local databases like

```text
SQLite File

inside one server
```

Use centralized databases

- PostgreSQL
- MySQL
- Managed Databases

---

# Mental Model

```text
Server

↓

Processing Only

↓

No Permanent User Data
```

Servers become disposable.

You can create or destroy them anytime.

---

# Load Balancer

## What is a Load Balancer?

A Load Balancer receives all incoming requests

and decides

> **Which backend server should handle each request.**

```text
Clients

↓

Load Balancer

↓

Server A

Server B

Server C
```

---

# Responsibilities

- Receive requests
- Choose server
- Forward request
- Return response
- Avoid overloaded servers
- Ignore failed servers

---

# Why We Need It

Without a Load Balancer

Clients must know

which server to contact.

With many servers,

this quickly becomes impossible.

The Load Balancer hides this complexity.

---

# Load Balancing Algorithms

---

## 1. Round Robin

Requests are distributed in order.

```text
1 → A

2 → B

3 → C

4 → A

5 → B
```

Best when

- Servers have equal capacity
- Requests have similar cost

Very simple.

---

## Problem with Round Robin

Not every request costs the same.

Example

```text
Request A

Simple Read

200 ms
```

```text
Request B

Database Write

External API

2 seconds
```

Round Robin doesn't know the difference.

One server may accidentally receive many expensive requests.

---

## 2. Weighted Round Robin

Servers with larger capacity receive more traffic.

Example

```text
Server A

8 CPU

↓

Weight = 2
```

```text
Server B

4 CPU

↓

Weight = 1
```

```text
Server C

4 CPU

↓

Weight = 1
```

Traffic

```text
A

A

B

C

A

A

B

C
```

More powerful servers perform more work.

---

## 3. Least Connections

Instead of rotating,

the Load Balancer checks

```text
Which server currently has the fewest active requests?
```

That server gets the next request.

---

### Why It Works Better

Suppose

```text
Server A

Processing

2-second Request
```

```text
Server B

Finished

Ready
```

Instead of sending another request to Server A,

the Load Balancer chooses Server B.

Heavy requests naturally spread out.

---

## Other Algorithms

Examples

- Least Response Time
- Resource-Based Routing
- CPU Utilization
- Memory Utilization

All try to send traffic

towards the healthiest servers.

---

# Active Connections

While a request is being processed,

the connection remains active.

Expensive requests

↓

Longer active connection

↓

Server appears busier

↓

Least Connections avoids sending more work there.

---

# Health Checks

What if a server crashes?

Without health checks

```text
Load Balancer

↓

Still Sends Requests

↓

Failed Server

↓

502 / 503 Errors
```

---

# Health Check Mechanism

The Load Balancer periodically sends

a small request.

Example

```text
GET /health
```

Expected response

```text
200 OK
```

---

## Healthy Server

```text
Health Check

↓

200 OK

↓

Continue Sending Traffic
```

---

## Failed Server

```text
Health Check

↓

Timeout / Error

↓

Remove From Rotation
```

No user traffic is sent there.

---

## Recovery

Health checks continue.

Once

```text
200 OK
```

is received again,

the server automatically rejoins the cluster.

---

# Why Health Checks Matter

Users should never notice

that one backend server failed.

Healthy servers continue serving traffic.

---

# Horizontal Scaling Checklist

✅ Stateless backend

✅ Shared sessions

✅ Shared file storage

✅ Shared database

✅ Load balancer

✅ Health checks

---

# Best Practices

- Keep backend servers stateless.
- Never store sessions in server memory.
- Store uploads in object storage.
- Use centralized databases.
- Put a Load Balancer in front of servers.
- Use Least Connections when request costs vary.
- Continuously perform health checks.

---

# Interview Takeaways

Know

- Statelessness
- Stateful Server
- Load Balancer
- Round Robin
- Weighted Round Robin
- Least Connections
- Active Connection
- Health Check
- Shared Session Store
- Shared Object Storage

---

# Mental Model

```text
User

↓

Load Balancer

↓

Chooses Healthy Server

↓

Stateless Backend

↓

Shared Storage

↓

Response
```

**Golden Rule**

> **Horizontal scaling only works when servers are stateless. Treat backend servers as replaceable workers and store all important state in shared infrastructure.**

# LEC 16F - Scaling & Performance (Part 6) - Database Scaling

# Why Databases Are Hard to Scale

Scaling application servers is relatively easy.

```text
More Traffic

↓

Add More Backend Servers
```

Databases are different because they store

- User data
- Orders
- Transactions
- Sessions
- Business data

This data must remain **consistent** across all instances.

That is what makes database scaling difficult.

---

# Two Major Database Scaling Techniques

```text
Database Scaling

├── Read Replicas

└── Sharding
```

---

# 1. Read Replicas

## Idea

Create copies of the primary database.

```text
                 Primary Database
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
   Replica A       Replica B       Replica C
```

The replicas contain the same data as the primary.

---

# Responsibility

## Primary Database

Handles

- INSERT
- UPDATE
- DELETE

All write operations.

---

## Read Replicas

Handle

- SELECT

Only read operations.

They never modify data.

---

# Why Read Replicas Help

Suppose

```text
100 Requests

↓

70 Reads

30 Writes
```

Without replicas

```text
Primary Database

↓

100 Requests
```

With replicas

```text
Primary

↓

30 Writes
```

```text
Replicas

↓

70 Reads
```

The primary database now performs much less work.

---

# Geographic Advantage

Read replicas are often deployed closer to users.

Example

```text
Primary

US
```

```text
Replica

India
```

```text
Replica

Japan
```

Users read data from nearby replicas,

reducing network latency.

---

# Problem — Replication Lag

Replicas are **copies**.

They do not receive updates instantly.

There is always a small delay.

```text
User Updates Name

↓

Primary Updated

↓

Replication

↓

Replica Updated
```

During this delay,

the replica still contains old data.

---

# Example

User changes

```text
"Alice"

↓

"Alicia"
```

Immediately refreshes the page.

The refresh is a read request.

If it reaches a replica

before replication finishes,

the replica still returns

```text
Alice
```

instead of

```text
Alicia
```

This creates confusing behavior.

---

# Replication Lag

```text
Primary Updated

↓

200 ms Delay

↓

Replica Updated
```

This delay is called

**Replication Lag**.

It is unavoidable because

data must physically travel between servers.

---

# Why It Happens

Even the fastest networks

cannot beat physics.

Data still needs time to move

between data centers.

---

# Handling Replication Lag

## Option 1

After a write,

temporarily send reads

to the primary database.

```text
Write

↓

Primary

↓

Next Read

↓

Primary
```

Once replicas catch up,

reads return to replicas.

---

## Option 2

Wait briefly

before serving reads.

```text
Write

↓

Wait

↓

Replication Complete

↓

Read
```

---

## Option 3

Delay the frontend refresh.

Instead of fetching immediately,

wait a few hundred milliseconds.

---

# Trade-off

Read replicas improve

- Scalability
- Latency

but introduce

- Eventual consistency

Every distributed system

must balance these trade-offs.

---

# 2. Sharding

## Problem

Imagine

```text
Orders Table

↓

10 Billion Rows
```

Even with indexes,

queries eventually become expensive.

---

# What is Sharding?

Instead of one huge database,

split the data

across multiple databases.

```text
Orders

↓

Shard A

↓

Shard B

↓

Shard C
```

Each shard stores

only part of the data.

---

# Example

Split by months.

```text
January - June

↓

Shard 1
```

```text
July - December

↓

Shard 2
```

Each database stores only half the data.

---

# Benefits

Instead of searching

10 billion rows,

each database searches

5 billion rows.

Queries become faster.

---

# Shard Key

A **Shard Key**

decides

which database stores a record.

Examples

- User ID
- Country
- Order Date
- Customer ID

Choosing a good shard key

is one of the hardest design decisions.

---

# Request Flow

```text
Backend

↓

Determine Shard

↓

Correct Database

↓

Response
```

The application must know

where the data lives.

---

# Benefits of Sharding

- Smaller databases
- Faster queries
- More storage capacity
- Better scalability
- Higher throughput

---

# Challenges

- Choosing shard key
- Cross-shard queries
- Data migration
- Rebalancing
- Increased complexity

---

# Modern Managed Databases

Today,

many providers manage

these complexities automatically.

Examples

- Amazon RDS
- PlanetScale
- Neon
- CockroachDB
- YugabyteDB

They provide

- Replication
- Backups
- Failover
- Scaling

without requiring developers

to build everything manually.

---

# As a Backend Engineer

Usually you

- Choose a provider.
- Configure replicas.
- Configure backups.
- Configure regions.

The provider manages

most infrastructure details.

Still,

understanding the concepts

is essential for interviews

and production debugging.

---

# Read Replicas vs Sharding

| Feature | Read Replicas | Sharding |
|---------|---------------|-----------|
| Goal | Increase read capacity | Split large datasets |
| Data | Same data in every replica | Different data in each shard |
| Reads | Distributed | Routed to correct shard |
| Writes | Primary only | Depends on shard |
| Biggest Challenge | Replication lag | Choosing shard key |

---

# Best Practices

- Scale reads with replicas.
- Scale massive datasets with sharding.
- Expect replication lag.
- Choose shard keys carefully.
- Use managed database services.
- Understand consistency trade-offs.

---

# Interview Takeaways

Know

- Primary Database
- Read Replica
- Replication
- Replication Lag
- Eventual Consistency
- Sharding
- Partitioning
- Shard Key
- Managed Databases

---

# Mental Model

```text
Application

↓

Need More Reads?

↓

Read Replicas

────────────

Need More Storage?

↓

Sharding

↓

Multiple Databases
```

**Golden Rule**

> **Use Read Replicas to scale reads, use Sharding to scale data volume, and remember that every distributed database optimization introduces consistency trade-offs.**


# LEC 16G - Scaling & Performance (Part 7) - Content Delivery Networks (CDNs)

# Why CDNs Exist

Even if your backend is highly optimized,

users far away from your servers still experience latency.

Why?

Because of **physics**.

Data cannot travel faster than the speed of light through fiber optic cables.

```text
User (Tokyo)

↓

Internet (~20,000 km)

↓

Server (Virginia, USA)

↓

Internet

↓

User
```

Even with a perfect backend,

this round trip already takes significant time. :contentReference[oaicite:0]{index=0}

---

# Network Latency Cannot Be Eliminated

Suppose the network itself takes

```text
100 ms
```

Now add

- Request parsing
- Business logic
- Database queries
- External API calls
- Response serialization

```text
Network

100 ms

+

Backend

200 ms

+

Database

100 ms

+

External API

200 ms

↓

500–800 ms
```

Even an optimized backend cannot remove the network delay. :contentReference[oaicite:1]{index=1}

---

# The Problem

Imagine

```text
Users

Japan

India

Australia

Brazil
```

All requests travel to

```text
One Server

Virginia (USA)
```

Everyone far away experiences higher latency.

---

# Solution — CDN

A **Content Delivery Network (CDN)** stores copies of content in many locations worldwide.

Instead of

```text
User

↓

USA
```

we have

```text
User

↓

Nearby CDN Server
```

The physical distance becomes much smaller.

---

# CDN Architecture

```text
                    Origin Server

                         │

      ┌──────────────────┼──────────────────┐

      ▼                  ▼                  ▼

 CDN India          CDN Japan          CDN Europe

      ▲                  ▲                  ▲

      │                  │                  │

 Users Nearby      Users Nearby      Users Nearby
```

The CDN sits closer to users than the origin server.

---

# Origin Server

The **Origin Server** is your actual backend.

It contains the original files and data.

If a CDN doesn't have a file,

it fetches it from the origin,

stores a copy,

and serves future users locally.

---

# Cache Hit

```text
User

↓

CDN

↓

File Exists

↓

Immediate Response
```

Very fast.

No request reaches your backend.

---

# Cache Miss

```text
User

↓

CDN

↓

File Missing

↓

Origin Server

↓

Store Copy

↓

Return Response
```

Future requests become faster.

---

# What Should Be Served by a CDN?

Perfect candidates

- Images
- CSS
- JavaScript
- Fonts
- Videos
- PDFs
- Downloads

These files change rarely

and are requested frequently.

---

# Dynamic Content

User-specific data usually should **not** be cached globally.

Examples

- Bank balance
- Shopping cart
- Private profile
- Notifications

These still come from your backend.

---

# Benefits of CDNs

## Lower Latency

Users download files from nearby servers.

---

## Reduced Backend Load

Instead of

```text
100,000 Image Requests

↓

Backend
```

we get

```text
100,000 Requests

↓

CDN

↓

Few Requests Reach Backend
```

The origin server performs much less work.

---

## Higher Availability

If one CDN location has issues,

traffic can often be routed through another nearby edge location.

---

## Better Scalability

Popular files can be served to millions of users

without overwhelming the backend.

---

# CDN vs Backend Cache

## Backend Cache (Redis)

Purpose

```text
Speed up

Database Access
```

Usually exists

inside your infrastructure.

---

## CDN Cache

Purpose

```text
Speed up

Global Content Delivery
```

Usually exists

near the users.

---

# Comparison

| Redis Cache | CDN |
|-------------|-----|
| Inside backend | Worldwide edge locations |
| Speeds database reads | Speeds file delivery |
| Mostly dynamic data | Mostly static content |
| Backend optimization | Global optimization |

---

# Real-World Examples

Popular CDN providers

- Cloudflare
- Amazon CloudFront
- Fastly
- Akamai

Most production applications use one of them.

---

# Request Flow

Without CDN

```text
Browser

↓

Origin Server

↓

Browser
```

With CDN

```text
Browser

↓

Nearest CDN

├── Cache Hit

│      ↓

│   Browser

│

└── Cache Miss

       ↓

Origin Server

       ↓

Store

       ↓

Browser
```

---

# Best Practices

- Cache static assets globally.
- Keep dynamic user data on the backend.
- Reduce geographical latency.
- Use CDN together with backend caching.
- Let the origin server handle only necessary requests.

---

# Interview Takeaways

Know

- CDN
- Edge Server
- Origin Server
- Cache Hit
- Cache Miss
- Global Caching
- Static Assets
- Network Latency

---

# Mental Model

```text
Far Away Users

↓

Nearest CDN

↓

(Cache Hit?)

├── Yes → Response

└── No

      ↓

Origin Server

      ↓

Cache

      ↓

Response
```

**Golden Rule**

> **Backend optimization reduces processing time, while CDNs reduce travel time. A fast backend alone cannot overcome the physical distance between users and servers.**

# LEC 16H - Scaling & Performance (Part 8) - Final CDN Concepts & Production Mindset

# CDN is Another Layer of Caching

Earlier we cached data

```text
Application

↓

Redis

↓

Database
```

Now we cache content

**before the request even reaches our backend.**

```text
User

↓

CDN

↓

Backend
```

This is called **Edge Caching**.

---

# Edge Servers

A CDN consists of many

**Edge Servers**

distributed across the world.

```text
Origin Server (USA)

↓

Edge India

↓

Edge Japan

↓

Edge Europe
```

Users automatically communicate with

their nearest edge server.

---

# Request Flow

## First Request

```text
Browser

↓

Nearest Edge

↓

Cache Miss

↓

Origin Server

↓

Store Copy

↓

Return Response
```

---

## Future Requests

```text
Browser

↓

Nearest Edge

↓

Cache Hit

↓

Immediate Response
```

The backend is never contacted.

---

# Why This Matters

Suppose

```text
10 Million Users

↓

Same JavaScript File
```

Without CDN

```text
10 Million Requests

↓

Origin Server
```

With CDN

```text
First Request

↓

Origin

↓

Cached

↓

Remaining Requests

↓

Nearest Edge
```

The backend workload becomes dramatically smaller.

---

# Static vs Dynamic Content

## Static Content ✅

Perfect CDN candidates

- Images
- CSS
- JavaScript
- Fonts
- Videos
- PDFs
- Downloads

These rarely change.

---

## Dynamic Content ❌

Avoid caching globally

- User Profile
- Shopping Cart
- Notifications
- Payment Data
- Bank Balance
- Personalized Dashboard

These should come directly from your backend.

---

# Multi-Level Caching

A modern backend usually has

multiple cache layers.

```text
Browser Cache

↓

CDN

↓

Redis

↓

Database
```

Each layer removes work

from the layer below it.

---

# Performance Strategy

Never jump directly to scaling.

The order should be

```text
Measure

↓

Optimize Queries

↓

Add Indexes

↓

Cache

↓

Scale Application

↓

Scale Database

↓

Use CDN

↓

Keep Monitoring
```

Scaling is the **last** step,

not the first.

---

# Production Mindset

Every optimization introduces trade-offs.

Examples

| Optimization | Trade-off |
|--------------|-----------|
| Cache | Stale data |
| Read Replica | Replication lag |
| Sharding | More complexity |
| CDN | Cache invalidation |
| Horizontal Scaling | Stateless architecture |

There is no "free" optimization.

Every improvement costs something.

---

# The Big Picture

A production backend is built from

multiple layers working together.

```text
Users

↓

CDN

↓

Load Balancer

↓

Stateless Backend Servers

↓

Redis Cache

↓

Primary Database

↓

Read Replicas

↓

Storage
```

Every component exists

to solve one specific bottleneck.

---

# Backend Performance Workflow

When performance drops,

don't guess.

Instead ask

```text
Where is the bottleneck?
```

Possible answers

- CPU
- Memory
- Network
- Database
- External APIs
- Disk
- Serialization
- Lock contention

Only optimize

after measuring.

---

# Complete Mental Model of Lecture 16

```text
Slow System

↓

Measure

↓

Find Bottleneck

↓

Optimize Queries

↓

Indexes

↓

Caching

↓

Profile Again

↓

Stateless Services

↓

Load Balancer

↓

Horizontal Scaling

↓

Read Replicas

↓

Sharding

↓

CDN

↓

Monitor Continuously
```

---

# Complete Interview Revision

You should now understand

### Performance

- Latency
- Throughput
- Utilization
- Percentiles
- Profiling
- Bottlenecks

### Database Optimization

- N+1 Queries
- JOINs
- Indexes
- Pagination
- Query Optimization

### Caching

- Cache Hit
- Cache Miss
- TTL
- Cache Aside
- Write Through
- Cache Stampede
- Cache Eviction

### Scaling

- Vertical Scaling
- Horizontal Scaling
- Stateless Services
- Shared Sessions
- Shared Storage

### Load Balancing

- Round Robin
- Weighted Round Robin
- Least Connections
- Health Checks

### Database Scaling

- Primary Database
- Read Replicas
- Replication Lag
- Eventual Consistency
- Sharding
- Shard Key

### Global Performance

- CDN
- Edge Server
- Origin Server
- Static Content
- Dynamic Content

---

# Final Golden Rules

1. **Measure before optimizing.**

2. **Optimize before scaling.**

3. **Keep backend servers stateless.**

4. **Cache expensive reads.**

5. **Scale databases carefully—they are the hardest component to scale.**

6. **Use CDNs to reduce physical distance, not backend processing time.**

7. **Every optimization has a trade-off.**

8. **A scalable system is built layer by layer—not by adding bigger servers.**

---

# Final Mental Model

```text
Performance Problems

↓

Measure

↓

Optimize

↓

Cache

↓

Scale

↓

Distribute

↓

Monitor

↓

Repeat
```

**Ultimate Golden Rule**

> **Performance engineering is an iterative process. Don't guess, don't prematurely optimize, and don't scale blindly. Measure the bottleneck, solve the right problem, and repeat as your system grows.**


