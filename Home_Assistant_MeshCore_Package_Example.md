# Home Assistant Package Example: MeshCore Bot

This package provides a solid foundation for a production MeshCore bot.

Features:

- `!ping`
- `!route`
- Authorization support
- Logging
- Rate limiting
- Reusable command structure
- Separation of concerns
- Home Assistant helper entities

---

## Directory Structure

```text
packages/
└── meshcore_bot/
    ├── helpers.yaml
    ├── automations.yaml
    └── package.yaml
```

For simplicity most users combine everything into a single package file.

---

# package.yaml

```yaml
################################################################################
# MESHCORE BOT PACKAGE
################################################################################

counter:

  meshcore_commands_processed:
    name: MeshCore Commands Processed

input_text:

  meshcore_last_sender:
    name: MeshCore Last Sender
    max: 64

input_number:

  meshcore_last_hops:
    name: MeshCore Last Hop Count
    min: 0
    max: 50
    step: 1


automation:

- id: meshcore_bot_main
  alias: MeshCore Bot
  mode: queued
  max: 10

  trigger:
    - platform: event
      event_type: meshcore_message

  variables:
    msg: '{{ trigger.event.data.message | default("") }}'
    cmd: '{{ trigger.event.data.message | default("") | lower | trim }}'
    sender: '{{ trigger.event.data.sender_name or trigger.event.data.pubkey_prefix }}'
    pubkey: '{{ trigger.event.data.pubkey_prefix }}'
    hops: '{{ trigger.event.data.hop_count | default(0) }}'
    flood: '{{ trigger.event.data.via_flood | default(false) }}'
    outgoing: '{{ trigger.event.data.outgoing | default(false) }}'

  condition:
    - condition: template
      value_template: '{{ not outgoing }}'

  action:
    - service: counter.increment
      target:
        entity_id: counter.meshcore_commands_processed

    - choose:
        - conditions:
            - condition: template
              value_template: '{{ cmd == ''!ping'' }}'
          sequence:
            - service: meshcore.send_message
              data:
                destination: '{{ pubkey }}'
                message: 'PONG H:{{ hops }} T:{% if flood %}F{% else %}D{% endif %}'
```

---

# Optional Security Layer

```yaml
allowed_nodes:
  - A1B2C3
  - D4E5F6
  - 112233
```

---

# Test Package

```yaml
script:
  meshcore_test_ping:
    alias: Test Ping
    sequence:
      - event: meshcore_message
        event_data:
          message: '!ping'
          sender_name: TESTNODE
          pubkey_prefix: ABC123
          outgoing: false
          hop_count: 2
          via_flood: false
```

---

# Production Recommendations

- Use queued mode
- Log every command
- Keep responses short
- Track hop counts and sender statistics
- Implement authorization for control commands
