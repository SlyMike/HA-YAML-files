# MeshCore Bot SDK Specification

Version: 1.0.0
Status: Draft
Target Platform: Home Assistant + MeshCore

---

# 1. Purpose

The MeshCore Bot SDK defines a standard architecture for creating portable, upgradeable, and redistributable MeshCore bots.

Goals:

- Command portability
- Standardized event handling
- Plugin-based extensions
- Backward compatibility
- HACS readiness
- Community contribution support
- Minimal configuration for first-time users

The SDK should allow developers to create new commands without modifying core logic.

---

# 2. Design Principles

The SDK shall:

- Treat commands as plugins
- Separate configuration from implementation
- Support safe upgrades
- Remain compatible with Home Assistant packages
- Support YAML-first deployments
- Allow future migration to a custom integration

---

# 3. Repository Structure

```text
meshcore-sdk/
│
├── core/
│   ├── dispatcher.yaml
│   ├── parser.yaml
│   ├── security.yaml
│   └── telemetry.yaml
│
├── commands/
│   ├── ping/
│   ├── route/
│   ├── status/
│   └── weather/
│
├── blueprints/
├── docs/
├── tests/
├── examples/
└── hacs.json
```

---

# 4. Event Contract

Every plugin receives a normalized payload.

```yaml
msg:
cmd:
sender:
publish_key:
hop_count:
flood:
message_type:
outgoing:
timestamp:
```

Missing values must be defaulted.

```yaml
hop_count: 0
flood: false
```

---

# 5. Core Variables API

Reserved variables:

```yaml
msg
cmd
sender
pubkey
hop_count
flood
message_type
outgoing
timestamp
```

These names are part of the SDK contract.

---

# 6. Command Registration Standard

Each command must declare:

```yaml
id: ping
command: !ping
version: 1.0.0
author: sdk
permission: public
```

---

# 7. Plugin Lifecycle

Stages:

1. Receive Event
2. Normalize Data
3. Authorize Sender
4. Match Command
5. Execute Plugin
6. Format Response
7. Send Message
8. Log Result

---

# 8. Permission Model

Supported levels:

```text
public
trusted
admin
owner
```

Example:

```yaml
permission: admin
```

Core enforces permissions before execution.

---

# 9. Response Contract

All commands return:

```yaml
success:
message:
metadata:
```

Example:

```yaml
success: true
message: PONG
metadata:
  hops: 2
```

---

# 10. Telemetry Contract

Standard metrics:

```text
Commands Processed
Failed Commands
Unknown Commands
Unauthorized Commands
Average Hop Count
```

---

# 11. Configuration Contract

User settings belong outside core code.

```yaml
enabled_commands:
trusted_nodes:
admin_nodes:
feature_flags:
```

---

# 12. Feature Flags

```yaml
mesh_diagnostics
weather
node_tracking
home_control
telemetry
```

Every non-core feature should have a toggle.

---

# 13. Testing Framework

## Unit Tests

Validate:

- Parsing
- Authorization
- Response formatting

## Integration Tests

Validate:

- Automation execution
- Event routing
- Service calls

## Regression Tests

Maintain fixed test vectors.

```text
!ping
!route
!status
```

Responses should remain unchanged.

---

# 14. Compatibility Requirements

SDK updates must:

- Preserve event fields
- Preserve reserved variable names
- Preserve command registration format
- Document breaking changes

---

# 15. Versioning

Semantic Versioning:

```text
MAJOR.MINOR.PATCH
```

Examples:

```text
1.0.0
1.1.0
2.0.0
```

---

# 16. Blueprint Standard

Every command package should include:

```text
README.md
command.yaml
tests.yaml
CHANGELOG.md
```

---

# 17. Community Contribution Rules

Pull requests must include:

- Documentation
- Test cases
- Upgrade notes
- Version update

---

# 18. HACS Readiness

Requirements:

- Semantic versions
- Release notes
- Stable repository structure
- Installation documentation
- Default configuration

---

# 19. Future Roadmap

Planned extension points:

- Node database API
- Route analytics API
- Weather API
- Telemetry storage abstraction
- Dashboard widgets
- AI-assisted command modules

---

# 20. Golden Rule

The core SDK must remain stable while commands evolve. New functionality should be delivered through plugins, feature flags, and configuration rather than modifications to the core framework.
