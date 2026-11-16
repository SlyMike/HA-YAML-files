# MeshCore Bot Development Guide v2

## Overview

This guide expands on the V1 clean-code guide and focuses on building production-ready MeshCore bots in Home Assistant with testing, validation, observability, and maintainability in mind.

---

# Architecture Principles

## Layer 1: Event Receiver

Responsible only for:

- Receiving meshcore_message events
- Initial validation
- Rejecting outbound/self-generated traffic

```yaml
trigger:
  - platform: event
    event_type: meshcore_message
```

---

## Layer 2: Parser

Normalize all input once.

```yaml
variables:
  cmd: >
    {{ trigger.event.data.message | lower | trim }}
```

Avoid repeatedly parsing the same message.

---

## Layer 3: Authorization

```yaml
authorized_nodes:
  - ABC123
  - DEF456
```

Only permit state-changing commands from approved nodes.

---

## Layer 4: Command Dispatcher

```yaml
choose:
  - conditions:
      - '{{ cmd == "!ping" }}'

  - conditions:
      - '{{ cmd == "!route" }}'
```

Keep command logic isolated.

---

# Standard Variable Block

```yaml
variables:

  msg: '{{ trigger.event.data.message | trim }}'

  cmd: '{{ trigger.event.data.message | lower | trim }}'

  sender: >
    {{ trigger.event.data.sender_name
      or trigger.event.data.pubkey_prefix }}

  target: '{{ trigger.event.data.pubkey_prefix }}'

  hop_count: >
    {{ trigger.event.data.hop_count | default(0) }}

  flood: >
    {{ trigger.event.data.via_flood | default(false) }}

  message_type: >
    {{ trigger.event.data.message_type | default('unknown') }}
```

---

# Logging Strategy

## Command Log

```yaml
service: logbook.log
data:
  name: MeshBot
  message: >
    CMD={{ cmd }}
    FROM={{ sender }}
```

## Security Log

```yaml
service: persistent_notification.create
data:
  title: Unauthorized Mesh Command
  message: >
    {{ sender }} attempted {{ cmd }}
```

---

# Testing Framework

## Test Categories

### Unit Tests

Validate individual commands.

Examples:

```text
!ping
!route
!status
```

Expected output should be deterministic.

### Integration Tests

Validate:

- Event reception
- Variable extraction
- Message transmission
- HA service calls

### Mesh Tests

Validate:

- Direct delivery
- Flood delivery
- Multi-hop delivery
- Sparse-network delivery

---

# Ping Test Matrix

| Test | Input | Expected |
|--------|--------|--------|
| Basic Ping | !ping | PONG response |
| Mixed Case | !PING | PONG response |
| Whitespace | !ping | PONG response |
| Flood Path | !ping | Flood shown |
| Direct Path | !ping | Direct shown |

---

# Route Test Matrix

| Test | Input | Verification |
|--------|--------|--------|
| Direct | !route | Direct displayed |
| Flood | !route | Flood displayed |
| Hop Count | !route | Correct hop count |
| Unknown Metadata | !route | Defaults applied |

---

# Fuzz Testing

Test malformed inputs.

Examples:

```text
!
!!
!unknown
12345
%%%%%%%%
```

Expected:

- No crash
- No unexpected response
- No automation failures

---

# Load Testing

Inject multiple commands.

```text
!ping
!ping
!ping
!ping
!ping
```

Verify:

- Queue behaviour
- No duplicate responses
- No automation lockups

Recommended:

```yaml
mode: queued
max: 5
```

---

# Security Testing

Verify:

- Unauthorized nodes denied
- Authorized nodes accepted
- Service calls protected
- No command injection

Never allow:

```text
!service light.turn_off
```

Only permit predefined commands.

---

# Telemetry Collection

Optional sensors:

```text
Last Heard Node
Commands Processed
Average Hop Count
Failed Commands
Unauthorized Attempts
```

Useful Home Assistant helpers:

- input_number
- input_text
- counter
- history_stats

---

# Recommended Project Layout

```text
packages/
└── meshbot/
    ├── diagnostics.yaml
    ├── routing.yaml
    ├── environment.yaml
    ├── security.yaml
    └── testing.yaml
```

---

# Continuous Improvement

Track:

- Most used commands
- Average hop count
- Failed responses
- Unauthorized access attempts
- Message delivery reliability

Review monthly and simplify commands that generate unnecessary traffic.

---

# Production Checklist

- Ignore outbound messages
- Normalize input
- Use authorization lists
- Implement rate limiting
- Log commands
- Log security events
- Test direct routing
- Test flood routing
- Test malformed input
- Test multi-hop messages
- Keep responses short
- Minimize airtime usage

---

# Gold Standard Philosophy

A production MeshCore bot should:

1. Respond predictably.
2. Never cause routing loops.
3. Never expose arbitrary Home Assistant services.
4. Minimize mesh traffic.
5. Fail safely.
6. Be testable.
7. Be easy to extend.

If a new feature cannot be tested, logged, and rate-limited, it should not be deployed into a live mesh.
