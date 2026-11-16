# MeshCore Home Assistant Bot to Redistributable Integration

## Best Practices Guide

### Purpose

This document describes how to evolve a MeshCore bot implemented as Home Assistant YAML automations into a redistributable Home Assistant package, integration, or add-on that can be deployed by other users with minimal configuration.

The primary goals are:

- Blank-slate deployment
- User customization without editing core code
- Upgrade safety
- Long-term maintainability
- Backward compatibility
- Community contribution friendliness

---

# Design Philosophy

Design the product as a framework rather than a bot.

Avoid:

- Hard-coded node IDs
- Hard-coded commands
- Fixed entities
- Assumptions about local sensors
- User-specific routing logic

Instead provide:

- Configuration helpers
- Enable/disable switches
- Command registration model
- Modular command packages
- Optional feature packs

A user should be able to install the project and immediately have a working bot without touching the source code.

---

# Package Structure

Recommended repository layout:

```text
meshcore-ha-bot/
│
├── README.md
├── CHANGELOG.md
├── LICENSE
├── CONTRIBUTING.md
│
├── packages/
│   ├── core.yaml
│   ├── diagnostics.yaml
│   ├── telemetry.yaml
│   ├── security.yaml
│   └── commands/
│       ├── ping.yaml
│       ├── route.yaml
│       └── status.yaml
│
├── blueprints/
│   └── automation/
│
├── docs/
│   ├── architecture.md
│   ├── events.md
│   ├── variables.md
│   └── migration.md
│
└── tests/
```

---

# Separate Core from User Configuration

Never store user configuration in core files.

Core package:

```yaml
!ping
!route
```

User configuration:

```yaml
allowed_nodes:
excluded_commands:
admin_nodes:
enabled_features:
```

Users should be able to upgrade without losing custom settings.

---

# Create Stable Internal APIs

Define standard variables.

```yaml
msg
cmd
sender
pubkey
hop_count
flood
message_type
```

Treat them as part of the public interface.

Future releases should preserve these names whenever possible.

---

# Configuration Driven Design

Avoid:

```yaml
if pubkey == ABC123
```

Prefer:

```yaml
input_text
input_boolean
input_select
```

and helper entities.

Benefits:

- GUI configurable
- Upgrade safe
- Easier support

---

# Implement Feature Flags

Every major subsystem should be optionally enabled.

Examples:

```yaml
Mesh Diagnostics
Weather Commands
Node Tracking
Home Control
Telemetry Collection
```

Use feature toggles.

```yaml
input_boolean.meshcore_enable_weather
```

---

# Versioning Strategy

Follow Semantic Versioning.

```text
1.0.0
1.1.0
1.2.0
2.0.0
```

Guidelines:

- Patch = bug fixes
- Minor = new features
- Major = breaking changes

---

# Migration Support

Provide migration guides.

Document:

- Removed commands
- Renamed helpers
- New permissions
- Changed event fields

Never force users to read commit history to upgrade.

---

# Testing Requirements

## Unit Tests

Validate:

- Command parsing
- Authorization
- Route logic
- Response formatting

## Regression Tests

Keep sample events.

```yaml
!ping
!route
!status
```

Verify responses remain unchanged after updates.

## Upgrade Tests

Install:

- Fresh deployment
- Previous release
- Development build

Ensure migration paths work.

---

# Backward Compatibility

Provide sensible defaults.

Example:

```yaml
hop_count | default(0)
```

Never assume fields exist.

Handle missing metadata gracefully.

---

# Observability

Track:

- Commands processed
- Failed commands
- Unknown commands
- Unauthorized requests
- Average hop count

Expose sensors where possible.

---

# Blueprint Strategy

Use Home Assistant blueprints for reusable automations.

Benefits:

- Easy deployment
- GUI driven configuration
- Reduced YAML editing

Blueprints are often easier for end users than package modifications.

---

# Documentation Standards

Every command should have:

```text
Description
Usage
Permissions
Examples
Responses
Configuration
```

Document all event fields.

Document all helper entities.

Document all known limitations.

---

# Security Model

Implement security from day one.

Support:

- Allow lists
- Admin lists
- Read-only users
- Command restrictions

Never expose arbitrary Home Assistant service execution.

---

# Extensibility Model

New commands should require only:

1. Command file
2. Registration entry
3. Help text

No core modifications.

This dramatically reduces future maintenance.

---

# Community Contribution Model

Provide templates.

Example:

```text
New Command Checklist
Coding Standards
Testing Requirements
Documentation Requirements
```

Require pull requests to contain tests.

---

# Roadmap Planning

Define extension areas:

```text
Mesh Diagnostics
Environmental Sensors
Weather Services
Node Tracking
Repeater Analytics
Telemetry Storage
Web Dashboard
```

This prevents architectural dead ends.

---

# Gold Standard Release Criteria

A release is considered production ready when:

- Clean install works
- Upgrade works
- Rollback works
- Documentation is complete
- Tests pass
- Default configuration works
- Security defaults are safe
- No hard-coded user values exist
- New commands are modular
- Users can customize behavior without editing core files

---

# Final Principle

Build a platform, not a bot.

The most successful open-source Home Assistant projects survive because core functionality is stable while user behavior remains highly configurable. Every design decision should favour portability, upgradeability, and customization over short-term convenience.
