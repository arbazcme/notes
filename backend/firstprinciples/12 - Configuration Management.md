# LEC 12 - Configuration Management

# What is Configuration Management?

Configuration Management is the process of managing all the settings that control how a backend application behaves.

Think of configuration as the **DNA of your application**.

It controls things like

- Database connections
- API keys
- Timeouts
- Feature flags
- Logging
- Security
- Performance
- Business rules

without changing application code.

---

# Why is Configuration Management Important?

Instead of hardcoding values,

store them separately from your code.

This allows

- Different environments
- Better security
- Easier deployments
- Easier maintenance
- Faster feature rollout

---

# Why Not Hardcode Values?

Hardcoding leads to

- Security risks
- Difficult deployments
- Code changes for simple settings
- Environment inconsistencies
- Configuration chaos

Bad

```text
Database Password

↓

Inside Source Code
```

Good

```text
Source Code

↓

Reads Configuration

↓

Runs Application
```

---

# Configuration Chaos

Without centralized configuration,

you may end up with

- Hardcoded values
- Duplicate settings
- Different behavior across environments
- Secret leaks
- Difficult debugging

---

# What Can Be Configured?

## 1. Application Settings

Examples

- Server port
- Log level
- Timeout values
- Connection pool size

---

## 2. Database Configuration

Examples

- Host
- Port
- Username
- Password
- Database name
- Connection timeout

---

## 3. External Services

Examples

- Stripe API keys
- Email provider keys
- Authentication provider keys
- Redis
- Elasticsearch
- Object storage

---

## 4. Feature Flags

Enable or disable features without deploying new code.

Example

```text
New Checkout

↓

Enabled only for US users
```

Useful for

- A/B Testing
- Gradual rollout
- Beta features
- Rollback

---

## 5. Security Configuration

Examples

- JWT secret
- Session secret
- Encryption keys
- Cookie settings

---

## 6. Performance Configuration

Examples

- Connection pool size
- Cache size
- Worker count
- CPU limits
- Request timeout

---

## 7. Business Rules

Examples

- Maximum order amount
- Daily withdrawal limit
- Discount percentage
- Session duration

These rules can change without modifying application code.

---

# Where Can Configurations Be Stored?

## Environment Variables ⭐ (Most Common)

Example

```text
DATABASE_URL

JWT_SECRET

REDIS_URL
```

Advantages

- Easy deployment
- Separate from code
- Supported by all languages
- Works well with containers

---

## Configuration Files

Common formats

- YAML
- JSON
- TOML

Example

```yaml
server:
  port: 8080

logging:
  level: info
```

Useful for

- Local development
- Application settings
- Readable configuration

---

## Secret Managers

Examples

- HashiCorp Vault
- AWS Parameter Store
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager

Best choice for production secrets.

Benefits

- Encryption
- Access control
- Secret rotation
- Audit logs

---

## Key-Value Stores

Examples

- Consul
- etcd

Useful for distributed systems where configuration changes dynamically.

---

# Hybrid Configuration

Large applications often combine multiple sources.

Example

```text
Secrets

↓

Secret Manager

Application Settings

↓

YAML

Environment Overrides

↓

Environment Variables
```

Priority can be defined by the application.

---

# Why Different Environments Need Different Configurations

The code remains the same.

Only the configuration changes.

---

## Development

Goal

Developer productivity.

Typical settings

- Debug logs
- Local database
- Small connection pool
- Mock services

---

## Testing

Goal

Automated testing.

Typical settings

- Test database
- Test APIs
- Fast execution
- Isolated environment

---

## Staging

Goal

Production-like environment.

Typical settings

- Similar production behavior
- Smaller infrastructure
- Used before deployment

---

## Production

Goal

Reliability, security, and performance.

Typical settings

- Production database
- Optimized connection pools
- Minimal logging
- Strong security

---

# Feature Flags

Feature flags allow runtime behavior changes.

Example

```text
Feature Enabled?

↓

Yes

↓

New Checkout

No

↓

Old Checkout
```

Advantages

- Gradual rollout
- Instant rollback
- A/B testing
- Regional rollout

---

# Security Best Practices

## Never Hardcode Secrets

Never commit

- API keys
- Passwords
- Tokens
- JWT secrets

to source code.

---

## Use Secret Managers

Production secrets should come from dedicated secret management systems.

---

## Encrypt Secrets

Secrets should be encrypted

- At rest
- In transit

---

## Least Privilege

Every service or developer should have access **only** to the configuration they need.

Example

```text
Frontend

↓

Frontend Config Only

Backend

↓

Backend Secrets

DevOps

↓

Infrastructure Secrets
```

---

## Rotate Secrets

Regularly rotate

- API keys
- Passwords
- Tokens
- Secrets

to reduce the impact of leaks.

---

# Validate Configuration at Startup ⭐

One of the most important backend practices.

Before the server starts,

verify

- Required variables exist
- Types are correct
- Values are valid
- Defaults are applied

If validation fails

```text
Application

↓

Startup

↓

Missing Config

↓

Fail Fast
```

Never allow missing configuration to cause runtime failures.

---

# Common Validation Examples

Validate

- Required variables
- URLs
- Numbers
- Timeouts
- Ports
- Boolean values

Example

```text
PORT

↓

Must be Integer

DATABASE_URL

↓

Must be Valid URL
```

---

# Configuration Loading Flow

```text
Application Starts

↓

Load Config

↓

Validate Config

↓

Create Runtime Settings

↓

Start Server
```

---

# Configuration in Distributed Systems

Modern backends connect to many services.

Examples

- Database
- Redis
- Message Queue
- Object Storage
- Authentication Provider
- Payment Gateway
- Email Service

Each integration requires configuration.

A centralized configuration system keeps all services consistent.

---

# Best Practices

- Keep configuration separate from code.
- Never hardcode secrets.
- Use environment variables.
- Store production secrets in secret managers.
- Validate configuration before startup.
- Encrypt secrets.
- Rotate secrets regularly.
- Apply least privilege.
- Use feature flags for gradual rollout.
- Keep different configurations for different environments.

---

# Interview Takeaways

Know

- What configuration management is
- Why hardcoding is bad
- Environment variables
- Secret managers
- Feature flags
- Development vs Staging vs Production
- Configuration validation
- Fail Fast principle
- Least Privilege
- Secret rotation

---

# Mental Model

```text
Configuration Sources

↓

Load

↓

Validate

↓

Runtime Configuration

↓

Application Starts
```

**Golden Rule**

> Keep code constant. Change application behavior through configuration—not by modifying the source code.
