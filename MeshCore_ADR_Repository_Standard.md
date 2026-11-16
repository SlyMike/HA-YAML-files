# MeshCore Architecture Decision Record (ADR) Repository Standard

Version: 1.0.0
Status: Draft Standard
Applies To: MeshCore Bot SDK, Home Assistant Packages, Integrations, HACS Projects, Command Plugins

---

# 1. Purpose

Architecture Decision Records (ADRs) are permanent documents that capture significant technical and architectural decisions.

The goal is to preserve institutional knowledge and explain:

- Why a decision was made
- What alternatives were considered
- What trade-offs were accepted
- How future contributors should interpret the decision

ADRs prevent repeated debates and reduce architectural drift.

---

# 2. Core Principles

Every major architectural decision shall:

- Have an ADR
- Be immutable after acceptance
- Remain permanently discoverable
- Link to related MEPs
- Record consequences and risks

ADRs document reasoning, not implementation details.

---

# 3. Repository Structure

```text
/docs
└── adr/
    ├── README.md
    ├── 0001-project-vision.md
    ├── 0002-command-registration.md
    ├── 0003-permission-model.md
    ├── 0004-plugin-lifecycle.md
    ├── 0005-telemetry-strategy.md
    └── template.md
```

---

# 4. Numbering Standard

ADRs use sequential numbering.

```text
ADR-0001
ADR-0002
ADR-0003
```

Numbers are never reused.

Numbers remain assigned even if an ADR is superseded.

---

# 5. ADR Lifecycle

```text
Proposed
 ↓
Review
 ↓
Accepted
 ↓
Implemented
 ↓
Active
```

Alternative states:

```text
Rejected
Withdrawn
Superseded
Deprecated
```

---

# 6. ADR Template

Every ADR must contain:

```text
Title
Status
Date
Authors
Related MEPs
Related ADRs
```

Sections:

```text
Context
Decision
Alternatives Considered
Consequences
Migration Impact
Future Considerations
```

---

# 7. Required ADR Topics

Create ADRs for:

- Event schema changes
- Permission model changes
- Plugin architecture changes
- Storage architecture
- Telemetry collection
- External API dependencies
- Breaking compatibility decisions
- Security architecture

Do not create ADRs for routine bug fixes.

---

# 8. Decision Quality Requirements

An ADR must answer:

- What problem exists?
- Why is this solution preferred?
- Why were alternatives rejected?
- What technical debt is created?
- What risks remain?

---

# 9. Linking Standards

MEPs define proposed changes.

ADRs document accepted decisions.

Example:

```text
MEP-0015 Route Analytics API
    ↓
ADR-0012 Route Metadata Strategy
```

---

# 10. Compatibility Documentation

ADRs affecting compatibility shall include:

```text
Breaking Changes
Migration Path
Rollback Strategy
Compatibility Risks
```

---

# 11. Security ADRs

Security-related ADRs must document:

- Threat model
- Abuse scenarios
- Trust boundaries
- Authentication model
- Authorization model

Security ADRs should be reviewed prior to implementation.

---

# 12. Operational ADRs

Operational decisions include:

- Logging standards
- Telemetry retention
- Backup strategies
- Upgrade procedures
- Release channels

These should be documented separately from feature ADRs.

---

# 13. ADR Review Process

Reviewers should evaluate:

- Technical correctness
- Consistency with SDK goals
- Upgrade impact
- Security implications
- Community impact

---

# 14. Superseding ADRs

Never edit historical ADRs.

Instead:

```text
ADR-0010 Active
ADR-0024 Supersedes ADR-0010
```

Historical context must remain visible.

---

# 15. Repository README Requirements

The ADR directory README should include:

- ADR index
- Current active ADRs
- Superseded ADRs
- Writing guidance
- Template links

---

# 16. Starter ADR Set

Recommended initial ADRs:

```text
ADR-0001 Project Vision
ADR-0002 Event Contract
ADR-0003 Command Registration
ADR-0004 Plugin Lifecycle
ADR-0005 Permission Model
ADR-0006 Feature Flags
ADR-0007 Telemetry Collection
ADR-0008 Versioning Strategy
ADR-0009 Testing Framework
ADR-0010 HACS Distribution Model
```

---

# 17. Release Requirement

Major releases should include review of all active ADRs.

Any deviation from an accepted ADR should trigger:

- ADR update
- New ADR
- MEP proposal

---

# 18. Golden Rule

Code explains how the system works.

ADRs explain why it works that way.

A mature MeshCore ecosystem should allow a contributor joining years later to understand the reasoning behind every major architectural decision without relying on tribal knowledge.
