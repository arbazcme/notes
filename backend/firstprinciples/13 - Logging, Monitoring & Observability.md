# LEC 13 - Logging, Monitoring & Observability

# Why Do We Need Them?

Modern backend systems run across

- Multiple servers
- Multiple services
- Multiple regions
- Databases
- Caches
- Message queues
- External APIs

When something fails,

we need to answer

- What happened?
- When did it happen?
- Where did it happen?
- Why did it happen?
- Which request failed?

Logging, Monitoring, and Observability help answer these questions.

---

# Logging

Logging means **recording important events** that happen inside your application.

Think of logs as your application's diary.

Examples

- User logged in
- Order created
- Payment failed
- Database query failed
- API request received

Each log usually contains metadata like

- Timestamp
- User ID
- Request ID
- API Route
- Status Code
- Error Message
- Service Name

---

# Monitoring

Monitoring means **continuously measuring the health and performance** of your system.

Instead of recording individual events,

it watches overall system behavior.

Examples

- CPU Usage
- Memory Usage
- Request Rate
- Error Rate
- Response Time
- Database Connections
- Throughput

Monitoring answers

> "Is my system healthy right now?"

---

# Observability

Observability is the ability to understand **why** a system behaves the way it does.

Monitoring tells you

> Something is wrong.

Observability tells you

> What exactly went wrong and where.

A system is considered observable when you can determine its internal state using external outputs.

---

# The Three Pillars of Observability

## 1. Logs

Record individual events.

Answer

> What happened?

---

## 2. Metrics

Numerical measurements collected over time.

Examples

- Error Rate
- CPU Usage
- Requests per Second
- Response Time
- Memory Usage

Answer

> Is the system healthy?

---

## 3. Traces

Track the complete journey of a request through your system.

Example

```text
Client

↓

API Gateway

↓

Authentication

↓

Validation

↓

Service

↓

Repository

↓

Database

↓

Response
```

Answer

> Where did the request spend time?
>
> Which component failed?

---

# Logs vs Metrics vs Traces

| Logs | Metrics | Traces |
|------|----------|---------|
| Individual events | Numerical data | Request journey |
| Detailed | Aggregated | End-to-end |
| Debugging | Monitoring | Root cause analysis |

---

# How They Work Together

Typical debugging workflow

```text
Monitoring

↓

High Error Rate Alert

↓

Metrics

↓

Find Problem Area

↓

Logs

↓

See Error Details

↓

Trace

↓

Find Exact Component That Failed
```

Each complements the others.

---

# Logging Levels

## DEBUG

Very detailed information.

Used during development.

Examples

- Function execution
- Variable values
- Internal flow

Usually disabled in production.

---

## INFO

Normal application events.

Examples

- User logged in
- Order created
- Server started

---

## WARN

Something unexpected happened,

but the application can continue.

Examples

- Invalid password
- Deprecated API used
- Retry attempt

---

## ERROR

Operation failed.

Examples

- Database query failed
- API request failed
- Validation error

Requires attention.

---

## FATAL

Critical failure.

Application cannot continue.

Usually followed by

```text
Application Shutdown
```

---

# Structured vs Unstructured Logs

## Unstructured Logs

Human-readable.

Example

```text
User 101 logged in successfully
```

Best for

- Development
- Local debugging

---

## Structured Logs

Machine-readable.

Usually JSON.

Example

```json
{
  "level": "error",
  "userId": 101,
  "route": "/login",
  "status": 500
}
```

Best for

- Production
- Log processing
- Dashboards
- Searching

---

# Why JSON Logs in Production?

Log management tools can easily

- Search
- Filter
- Aggregate
- Visualize

structured JSON logs.

---

# What Should Be Logged?

Log important events like

- Authentication
- API requests
- Business events
- Database failures
- External API failures
- Background jobs
- Startup & shutdown
- Security events

Avoid logging

- Passwords
- JWT tokens
- API secrets
- Sensitive personal data

---

# Metrics

Metrics are numerical values describing system behavior.

Examples

Infrastructure Metrics

- CPU Usage
- Memory Usage
- Disk Usage

Application Metrics

- Requests per Second
- Error Rate
- Average Response Time
- Active Users

Business Metrics

- Orders Created
- Payments Completed
- Revenue
- Signups

Metrics help identify trends over time.

---

# Alerts

Monitoring systems can notify developers when thresholds are exceeded.

Example

```text
Error Rate > 80%

↓

Slack Alert

↓

Investigate
```

Common alerts

- High CPU
- Low Memory
- High Response Time
- High Error Rate
- Database Down

---

# Tracing

Tracing follows one request through every component.

Example

```text
Client

↓

Load Balancer

↓

Backend

↓

Validation

↓

Service

↓

Repository

↓

Database

↓

Response
```

Every request gets a unique **Trace ID**.

Each operation within that request creates a **Span**.

---

# Trace vs Span

```text
Trace

↓

Entire Request

Span

↓

One Operation
```

Example

```text
Trace

├── Authentication
├── Validation
├── Database Query
└── Redis Cache
```

This makes performance bottlenecks easy to identify.

---

# Instrumentation

Instrumentation means adding code that collects

- Logs
- Metrics
- Traces

from your application.

Without instrumentation,

observability tools have nothing to collect.

---

# OpenTelemetry

OpenTelemetry is the industry-standard framework for collecting

- Logs
- Metrics
- Traces

It provides SDKs for major languages like

- Node.js
- Go
- Python
- Java
- .NET

It is vendor-neutral,

so the collected data can be sent to different observability platforms.

---

# Popular Tools

## Open Source

- Prometheus → Metrics
- Grafana → Dashboards
- Loki → Logs
- Jaeger → Traces

Often called the **Grafana Stack**.

---

## Commercial Platforms

- New Relic
- Datadog

These provide

- Logs
- Metrics
- Traces
- Dashboards
- Alerts

in one integrated platform.

---

# Typical Observability Workflow

```text
Request

↓

Application

↓

Instrumentation

↓

Logs
Metrics
Traces

↓

Observability Platform

↓

Dashboard

↓

Alerts

↓

Developer Debugs Issue
```

---

# Development vs Production Logging

| Development | Production |
|-------------|------------|
| Debug Logs | Info & Error Logs |
| Human-readable | JSON Logs |
| Local Console | Centralized Log Platform |

---

# Best Practices

- Log meaningful events only.
- Never log sensitive data.
- Use structured logs in production.
- Monitor infrastructure and application metrics.
- Configure alerts for critical failures.
- Instrument important request paths.
- Use distributed tracing in microservices.
- Keep log levels appropriate for each environment.
- Correlate logs using Request ID / Trace ID.

---

# Interview Takeaways

Know

- Difference between Logging, Monitoring, and Observability
- Three pillars of observability
- Logs vs Metrics vs Traces
- Logging levels
- Structured vs unstructured logging
- Trace vs Span
- Instrumentation
- OpenTelemetry
- Prometheus, Grafana, Loki, Jaeger
- New Relic / Datadog
- Why monitoring alone is insufficient

---

# Mental Model

```text
User Request

↓

Instrumentation

↓

Logs
Metrics
Traces

↓

Observability Platform

↓

Dashboards + Alerts

↓

Developer Finds Root Cause
```

**Golden Rule**

> **Logging tells you what happened. Monitoring tells you when something is wrong. Observability helps you understand why it happened.**
