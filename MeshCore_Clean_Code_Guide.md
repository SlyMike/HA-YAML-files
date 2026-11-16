# Clean Code Guide for MeshCore Bots in Home Assistant

## Overview

This guide is aimed at building maintainable, low-traffic, mesh-friendly MeshCore bots using Home Assistant automations.

The recommendations are inspired by the official meshcore-ha integration, meshy, and meshcore-homeassistant-bridge community projects.

---

# Design Principles

A MeshCore bot is not a Discord bot.

Every message consumes:

- Airtime
- Battery power
- Network capacity
- Flood budget

Good bots:

- Short replies
- Prefer DMs over channels
- Rate limit responses
- Ignore malformed commands
- Reply once
- Avoid message loops
- Minimize flood traffic

Bad bots:

- Status spam
- Periodic broadcasts
- Multi-message responses
- Flooding route tests
- Echoing every message

---

# Recommended Project Structure

```yaml
!ping
!route
!weather
!status
!seen
!battery
```

Keep each command inside its own choose block.

---

# Common Event Variables

```yaml
- variables:

    msg: >
      {{ trigger.event.data.message | trim }}

    cmd: >
      {{ trigger.event.data.message
          | lower
          | trim }}

    target: >
      {{ trigger.event.data.pubkey_prefix }}

    sender: >
      {{
        trigger.event.data.sender_name
        or trigger.event.data.pubkey_prefix
      }}

    outgoing: >
      {{ trigger.event.data.outgoing }}

    hop_count: >
      {{ trigger.event.data.hop_count
           | default(0) }}

    flood: >
      {{ trigger.event.data.via_flood
           | default(false) }}
```

---

# Trigger Template

```yaml
trigger:
  - platform: event
    event_type: meshcore_message
```

## Ignore Outbound Messages

```yaml
condition:
  - condition: template
    value_template: >
      {{ not outgoing }}
```

---

# Command Parsing

```yaml
cmd: >
  {{ msg | lower | trim }}
```

This allows:

```text
!Ping
!PING
!ping
```

all to be treated the same.

---

# Direct vs Flood Awareness

```yaml
Method:
{% if flood %}
Flood
{% else %}
Direct
{% endif %}
```

Useful commands:

```text
!ping
!route
!trace
```

---

# Hop Count Usage

Example:

```text
PONG
Type: Direct
Hops: 2
```

Uses:

- Route troubleshooting
- Repeater positioning
- Network expansion
- Battery diagnostics

---

# Rate Limiting

```yaml
mode: queued
max: 5
```

or

```yaml
mode: single
```

---

# Message Length Control

Recommended:

```text
< 100 characters
```

Ideal:

```text
< 60 characters
```

Good:

```text
PONG D:2 H:3
```

---

# Authentication

Maintain an allowlist.

```yaml
allowed_nodes:
  - ABC123
  - DEF456
```

Validate sender identity before permitting control commands.

---

# Safe Home Assistant Control

Good:

```text
!light on
!light off
!garage
!gate
```

Avoid exposing unrestricted service execution.

---

# Logging

```yaml
service: logbook.log
data:
  name: MeshBot
  message: >
    CMD={{ cmd }}
    FROM={{ sender }}
```

---

# Recommended Commands

## Diagnostics

```text
!ping
!route
!status
!version
!uptime
```

## Network

```text
!nodes
!seen
!neighbors
!repeaters
!health
```

## Environmental

```text
!temp
!weather
!pressure
!rain
```

---

# Naming Standards

Use:

```text
!ping
!route
!weather
```

Avoid unnecessarily long command names.

---

# Future-Proof Architecture

Build around:

```yaml
msg
cmd
sender
target
hop_count
flood
```

These variables support almost every future command implementation.

---

# Gold Standard Example

```yaml
alias: MeshCore Bot

mode: queued

trigger:
  - platform: event
    event_type: meshcore_message

condition:
  - "{{ not trigger.event.data.outgoing }}"

action:

  - variables:

      cmd: >
        {{ trigger.event.data.message
             | lower
             | trim }}

      target: >
        {{ trigger.event.data.pubkey_prefix }}

      hops: >
        {{ trigger.event.data.hop_count
             | default(0) }}

      flood: >
        {{ trigger.event.data.via_flood
             | default(false) }}

  - choose:

      - conditions:
          - "{{ cmd == '!ping' }}"
        sequence:

          - service: meshcore.send_message
            data:
              destination: "{{ target }}"
              message: >
                PONG
                H:{{ hops }}
                T:{% if flood %}F{% else %}D{% endif %}
```

---

# Final Rule

Every transmitted character consumes mesh resources.

Keep responses short, deterministic, authenticated, and useful.
