# LEC 09 - Background Jobs & Task Queues

# What are Background Jobs?

A background job is a task that runs **outside the request-response cycle**.

Instead of making the user wait,

the work is done later by a separate worker.

---

# Why Use Background Jobs?

Without background jobs

```text
Client

↓

API

↓

Long-running Task

↓

Response
```

The user waits until everything finishes.

With background jobs

```text
Client

↓

API

↓

Push Task to Queue

↓

Immediate Response

↓

Worker

↓

Execute Task
```

The API stays fast and responsive.

---

# When Should You Use Background Jobs?

Use them when the work

- Takes time
- Depends on external services
- Doesn't need an immediate response

Examples

- Sending emails
- Image processing
- Video encoding
- PDF generation
- Push notifications
- Scheduled reports

---

# Email Verification Example

Instead of

```text
Signup

↓

Send Email

↓

Return Response
```

Do

```text
Signup

↓

Create Email Task

↓

Push to Queue

↓

Return Success

↓

Worker Sends Email
```

Benefits

- Faster signup
- Better user experience
- Easier retries if email service fails

---

# Main Components

## Producer

Creates the task.

Example

```text
Send Verification Email
```

Pushes it into the queue.

---

## Queue (Broker)

Temporary storage for tasks.

Stores tasks until workers process them.

Popular technologies

- RabbitMQ
- Redis Pub/Sub
- Amazon SQS

---

## Worker (Consumer)

Runs in a separate process.

Responsibilities

- Read task
- Execute task
- Report success/failure

---

# High-Level Workflow

```text
Client

↓

API

↓

Producer

↓

Task Queue

↓

Worker

↓

Execute Task

↓

External Service / Database
```

---

# Retries

Background job systems automatically retry failed jobs.

Example

```text
Try 1

↓

Fail

↓

Retry

↓

Fail

↓

Retry Again
```

A common strategy is

**Exponential Backoff**

Example

```text
1 min

↓

2 min

↓

4 min

↓

8 min
```

This prevents continuously hammering failing services.

---

# Why Background Jobs Improve Reliability

If an external service fails

Without background jobs

```text
API fails
```

With background jobs

```text
Task fails

↓

Retry

↓

Eventually succeeds
```

The user doesn't have to retry manually.

---

# Common Use Cases

## Email Sending

- Verification emails
- Password reset emails
- Welcome emails

---

## Image Processing

- Resize images
- Compress images
- Generate thumbnails

---

## Video Processing

- Encode videos
- Generate multiple resolutions
- Create thumbnails
- Generate subtitles

---

## Report Generation

Examples

- Daily reports
- Weekly reports
- Monthly reports
- PDF generation

---

## Push Notifications

Examples

- Food delivery updates
- Chat notifications
- Mobile notifications

---

# Task Queue Internals

Tasks are serialized

↓

Stored in queue

↓

Worker dequeues task

↓

Executes task

↓

Acknowledges completion

If acknowledgement isn't received,

the queue assumes the worker failed

and makes the task available again.

---

# Visibility Timeout

A task is considered

"in progress"

for a limited time.

If the worker crashes before finishing,

another worker can process the task.

This prevents losing jobs.

---

# Types of Background Jobs

## One-Off Jobs

Triggered once.

Examples

- Send email
- Reset password
- Welcome notification

---

## Recurring Jobs

Run on a schedule.

Examples

- Daily reports
- Weekly cleanup
- Monthly billing
- Database cleanup

---

## Chain Jobs

Tasks depend on previous tasks.

Example

```text
Upload Video

↓

Encode Video

↓

Generate Thumbnail

↓

Resize Thumbnail
```

Some child tasks can run in parallel.

---

## Batch Jobs

Execute many similar tasks together.

Examples

- Delete thousands of user records
- Send reports to all users
- Bulk notifications

---

# Important Design Principles

## Idempotency

A task should be safe to execute multiple times.

Example

If retry happens,

the result should remain correct.

Very common interview topic.

---

## Error Handling

Always

- Catch errors
- Log errors
- Retry recoverable failures

---

## Monitoring

Track

- Queue length
- Failed jobs
- Successful jobs
- Retry count
- Worker health

Popular tools

- Prometheus
- Grafana

---

## Scalability

Need more throughput?

Simply add more workers.

```text
Queue

↓

Worker 1

Worker 2

Worker 3

Worker 4
```

This is called **horizontal scaling**.

---

## Ordering

Some jobs must execute in sequence.

Example

```text
Encode Video

↓

Generate Thumbnail
```

The second task cannot start first.

---

## Rate Limiting

Workers calling external APIs should respect API limits.

Avoid overwhelming third-party services.

---

# Best Practices

- Keep tasks small.
- One task = one responsibility.
- Break large jobs into smaller jobs.
- Retry only recoverable failures.
- Make tasks idempotent.
- Monitor queues continuously.
- Log everything important.
- Scale workers horizontally.

---

# Interview Takeaways

- Background jobs run outside the request-response cycle.
- They improve responsiveness and scalability.
- Task Queue architecture:
  - Producer
  - Queue (Broker)
  - Worker (Consumer)
- Retries commonly use Exponential Backoff.
- Important concepts:
  - Idempotency
  - Visibility Timeout
  - Monitoring
  - Horizontal Scaling
- Common technologies:
  - Redis
  - RabbitMQ
  - Amazon SQS
  - BullMQ (Node.js)
  - Celery (Python)

---

# Mental Model

```text
Client

↓

API

↓

Push Job

↓

Queue

↓

Worker

↓

Execute

↓

Retry if Needed

↓

Success
```

The API should return quickly.

Anything slow or non-critical should be moved to a background job.
