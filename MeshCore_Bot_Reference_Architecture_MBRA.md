# MeshCore Bot Reference Architecture (MBRA)

Version: 1.0.0
Status: Reference Architecture
Scope: Home Assistant, MeshCore Bots, SDK Plugins, HACS Distribution

---

# Executive Summary

The MeshCore Bot Reference Architecture (MBRA) defines the canonical architecture for building, distributing, operating, and extending MeshCore bots within Home Assistant.

MBRA serves as the top-level architecture document that aligns:

- Clean Code Guide
- SDK Specification
- MEP Process
- ADR Repository
- Home Assistant Package Design
- Plugin Ecosystem
- Testing Framework
- HACS Distribution

The goal is to create a sustainable ecosystem rather than a collection of isolated bot projects.

---

# Architectural Goals

The architecture must support:

- Blank-slate installation
- Safe upgrades
- Modular commands
- Community contributions
- Long-term maintainability
- Backward compatibility
- Multiple deployment models

---

# Layer Model

```text
+------------------------------------------------+
| User Commands                                  |
+------------------------------------------------+
| Command Plugins                                |
+------------------------------------------------+
| SDK Command Dispatcher                         |
+------------------------------------------------+
| Security & Authorization                        |
+------------------------------------------------+
| Event Normalization Layer                      |
+------------------------------------------------+
| Home Assistant MeshCore Integration            |
+------------------------------------------------+
| MeshCore Network                               |
+------------------------------------------------+
```

---

# Core Components

## Mesh Layer

Responsible for:

- Message transport
- Flood routing
- Direct routing
- Hop propagation
- Network resilience

## Integration Layer

Responsible for:

- Event ingestion
- Message transmission
- Service abstraction

## Normalization Layer

Creates a standard SDK payload:

```yaml
msg:
cmd:
sender:
pubkey:
hop_count:
flood:
outgoing:
message_type:
timestamp:
```

---

# Security Architecture

Security is mandatory.

Trust Levels:

```text
Public
Trusted
Admin
Owner
```

Rules:

- Deny by default
- Explicit allow lists
- No arbitrary service execution
- No unrestricted scripting

---

# Command Architecture

Every command is a plugin.

Example:

```text
!ping
!route
!status
!weather
```

Plugin Structure:

```text
command.yaml
README.md
tests.yaml
CHANGELOG.md
```

Commands must not modify SDK core logic.

---

# Event Flow

```text
Mesh Message
    ↓
Event Trigger
    ↓
Normalization
    ↓
Authorization
    ↓
Command Match
    ↓
Plugin Execution
    ↓
Response Generation
    ↓
Telemetry Update
    ↓
Reply Sent
```

---

# Telemetry Architecture

Required Metrics:

```text
Commands Processed
Failed Commands
Unknown Commands
Unauthorized Attempts
Average Hop Count
```

Optional Metrics:

```text
Most Used Command
Route Quality
Node Activity
Message Latency
```

---

# Configuration Architecture

Configuration must be externalized.

Never hard-code:

```text
Node IDs
Entity IDs
Admin Lists
API Keys
```

Use:

```text
Helpers
Configuration Files
Feature Flags
Blueprint Inputs
```

---

# Plugin Architecture

Registration Contract:

```yaml
id:
command:
version:
permissions:
```

Plugins should be:

- Self-contained
- Testable
- Independently versioned

---

# Testing Architecture

## Unit Layer

Validate:

- Parsing
- Permissions
- Formatting

## Integration Layer

Validate:

- Event handling
- Automation execution
- Responses

## Regression Layer

Validate:

- Existing behaviour
- Previous releases

## Upgrade Layer

Validate:

- Migration paths
- Backward compatibility

---

# HACS Distribution Architecture

Repository Requirements:

```text
README.md
LICENSE
CHANGELOG.md
hacs.json
```

Release Requirements:

- Semantic versioning
- Upgrade documentation
- Test completion

---

# Governance Architecture

Project Governance Stack:

```text
Reference Architecture
        ↓
ADRs
        ↓
SDK
        ↓
MEPs
        ↓
Implementations
```

Reference Architecture defines direction.

ADRs define decisions.

MEPs define change proposals.

SDK defines implementation contracts.

---

# Scalability Roadmap

Phase 1:

```text
!ping
!route
!status
```

Phase 2:

```text
Node Tracking
Telemetry
Weather
```

Phase 3:

```text
Analytics
Dashboards
Historical Metrics
```

Phase 4:

```text
Dedicated Home Assistant Integration
Plugin Marketplace
```

---

# Migration Strategy

Current State:

```text
YAML Automation Bot
```

Intermediate State:

```text
SDK Package Framework
```

Target State:

```text
Native Integration
HACS Distribution
Plugin Ecosystem
```

Users should be able to migrate without rewriting commands.

---

# Quality Gates

A release must satisfy:

- Tests pass
- Documentation complete
- Upgrade safe
- Security reviewed
- ADRs updated
- No breaking contract violations

---

# Golden Principle

The MeshCore bot ecosystem should evolve by extending stable interfaces rather than modifying existing behaviour. Portability, upgradeability, customization, and compatibility take precedence over feature velocity.
