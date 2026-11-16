# MeshCore Route & Ping Bot

## Overview

This project extends the Home Assistant MeshCore integration to provide basic network diagnostics over the mesh.

Supported commands:

| Command | Function |
|----------|----------|
| `!ping` | Confirms the bot is reachable and reports message type and hop information |
| `!route` | Reports known route information for the sender and whether the message arrived via direct routing or flood routing |

The bot listens for incoming `meshcore_message` events and responds only to inbound messages.

---

# Objectives

Provide operators with:

- Reachability testing
- Route diagnostics
- Flood vs Direct path identification
- Hop count reporting
- Future framework for mesh health tools

---

# Expected Message Fields

```yaml
trigger.event.data:
  message:
  sender_name:
  pubkey_prefix:
  outgoing:
  message_type:
  hop_count:
  via_flood:
```

---

# Core Variables

```yaml
- variables:
    msg: "{{ trigger.event.data.message | lower | trim }}"
    target: "{{ trigger.event.data.pubkey_prefix }}"
    hops: "{{ trigger.event.data.hop_count | default(0) }}"
    flood: "{{ trigger.event.data.via_flood | default(false) }}"
    sender: >
      {{
        trigger.event.data.sender_name
        or trigger.event.data.pubkey_prefix
      }}
```

---

# !ping Command

```yaml
- conditions:
    - condition: template
      value_template: "{{ msg == '!ping' }}"
  sequence:
    - service: meshcore.send_message
      data:
        destination: "{{ target }}"
        message: >
          PONG

          Type:
          {% if flood %}
          Flood
          {% else %}
          Direct
          {% endif %}

          Hops: {{ hops }}
```

---

# !route Command

```yaml
- conditions:
    - condition: template
      value_template: "{{ msg == '!route' }}"
  sequence:
    - service: meshcore.send_message
      data:
        destination: "{{ target }}"
        message: >
          Route Information

          Node: {{ sender }}

          Method:
          {% if flood %}
          Flood
          {% else %}
          Direct
          {% endif %}

          Hop Count: {{ hops }}
```

---

# Combined Choose Block

```yaml
action:

  - variables:
      msg: "{{ trigger.event.data.message | lower | trim }}"
      target: "{{ trigger.event.data.pubkey_prefix }}"
      hops: "{{ trigger.event.data.hop_count | default(0) }}"
      flood: "{{ trigger.event.data.via_flood | default(false) }}"
      sender: >
        {{
          trigger.event.data.sender_name
          or trigger.event.data.pubkey_prefix
        }}

  - choose:

      - conditions:
          - condition: template
            value_template: "{{ msg == '!ping' }}"
        sequence:
          - service: meshcore.send_message
            data:
              destination: "{{ target }}"
              message: >
                PONG

                Type:
                {% if flood %}
                Flood
                {% else %}
                Direct
                {% endif %}

                Hops: {{ hops }}

      - conditions:
          - condition: template
            value_template: "{{ msg == '!route' }}"
        sequence:
          - service: meshcore.send_message
            data:
              destination: "{{ target }}"
              message: >
                Route Information

                Node: {{ sender }}

                Method:
                {% if flood %}
                Flood
                {% else %}
                Direct
                {% endif %}

                Hop Count: {{ hops }}
```
