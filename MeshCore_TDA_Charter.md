# MeshCore Technical Design Authority (TDA) Charter

Version: 1.0.0
Status: Foundational Governance Charter

## Purpose

The Technical Design Authority (TDA) is the governing body responsible for maintaining the long-term technical integrity, security, interoperability, and architectural consistency of the MeshCore Bot Framework ecosystem.

The TDA exists to ensure that:

- Architecture remains coherent.
- Breaking changes are controlled.
- Security risks are assessed.
- Community contributions align with project goals.
- SDK contracts remain stable.
- Releases are governed consistently.

---

## Scope

The TDA governs:

- MeshCore Bot SDK
- Home Assistant package framework
- Plugin standards
- MEP approvals
- ADR acceptance
- Security standards
- Release architecture
- Compatibility policies
- HACS distribution strategy

---

## Authority

The TDA has final authority over:

- Acceptance of new architectural standards
- Approval of breaking changes
- Deprecation decisions
- Security policy changes
- SDK contract modifications
- Core framework releases

The TDA does not control day-to-day development activities unless architectural, security, or compatibility concerns exist.

---

## Membership

Roles:

### Chief Architect

Responsibilities:

- Architecture ownership
- Final technical arbitration
- Strategic roadmap alignment

### Security Lead

Responsibilities:

- Threat modelling
- Vulnerability review
- Security release approvals

### SDK Maintainers

Responsibilities:

- SDK stewardship
- Compatibility validation
- Plugin ecosystem support

### Community Maintainers

Responsibilities:

- Documentation review
- Contributor support
- Community feedback collection

---

## Decision Categories

### Standard Decisions

Examples:

- New commands
- Documentation changes
- Testing improvements

Approval:

- Simple majority

### Architectural Decisions

Examples:

- Event schema changes
- Plugin lifecycle changes
- SDK contract changes

Approval:

- Majority plus Chief Architect approval

### Security Decisions

Examples:

- Permission model updates
- Authentication changes
- Risk mitigations

Approval:

- Security Lead approval required

---

## MEP Governance

All significant changes begin as MEPs.

Workflow:

Draft → Review → Accepted → Implemented → Active

TDA responsibilities:

- Review proposals
- Evaluate impact
- Approve or reject
- Ensure compatibility planning
- Confirm testing strategy

---

## ADR Governance

Accepted architectural decisions must be recorded as ADRs.

Requirements:

- ADRs are immutable
- Superseding ADRs create new records
- Historical context is preserved

---

## Release Governance

Release gates:

- Tests passing
- Documentation updated
- Security review completed
- ADR updates completed
- Migrations documented

Major releases require explicit TDA approval.

---

## Compatibility Policy

The TDA shall prioritize:

1. Backward compatibility
2. Upgrade safety
3. Data preservation
4. Stable APIs

Breaking changes require:

- Documented justification
- Migration plan
- Version increment

---

## Security Response Process

Severity Levels:

- Critical
- High
- Medium
- Low

Critical vulnerabilities may bypass normal release schedules.

Emergency releases require:

- Security Lead validation
- Maintainer notification
- Post-incident review

---

## Community Participation

Community members may:

- Submit MEPs
- Submit ADR proposals
- Contribute code
- Review documentation
- Report vulnerabilities

Governance should remain transparent and publicly documented.

---

## Transparency Requirements

The project shall publish:

- Release notes
- ADR index
- Accepted MEPs
- Roadmaps
- Deprecation notices

---

## Success Measures

The TDA is successful when:

- Upgrades remain predictable
- SDK contracts remain stable
- Security incidents are addressed rapidly
- Community participation remains healthy
- Architectural quality is maintained over time

---

## Amendments

Changes to this charter require:

- Formal proposal
- TDA review
- Recorded rationale
- Published revision history

---

## Foundational Principle

The role of the TDA is not to maximize feature delivery. Its role is to maximize long-term ecosystem health, security, stability, and maintainability while enabling sustainable community growth.
