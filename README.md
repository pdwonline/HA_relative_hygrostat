[![hacs_badge](https://img.shields.io/badge/HACS-Custom-41BDF5.svg)](https://github.com/hacs/integration) [![made-with-python](https://img.shields.io/badge/Made%20with-Python-1f425f.svg)](https://www.python.org/) [![hacs_badge](https://img.shields.io/badge/Buy-Me%20a%20Coffee-critical)](https://www.buymeacoffee.com/pdwonline)

# HA_relative_hygrostat
Home Assistant binary hygrostat based on humidity trend

This is actually a fork of [github.com/basschipper/homeassistant-generic-hygrostat](https://github.com/basschipper/homeassistant-generic-hygrostat). Since there is a name conflict with the introduced HA integration [Generic Hygrostat](https://www.home-assistant.io/integrations/generic_hygrostat/), I renamed the component and made it available here.

## How does this differ from the HA Generic Hygorostat?
This implementation switches based on a relitive humidity trend, whereas the generic HA hygrostat switches between a static humidity range. Due to the fact that humidity levels are different during the summer and winter, a static humidity level switch is very complicated and requires outside temperature and humidity sensors to be involved. This binary_sensor detects just the high rises in humidity and switches on. It switches off when the humidity is back to normal. Therefore, it is very usefull for controlling a fan based on humidity.

## Setup
In your `configuration.yaml` you'll need:

```yaml
binary_sensor:
- platform: relative_hygrostat
  name: Bathroom Hygrostat
  sensor: sensor.bathroom_climate_humidityy # Source humidity sensor
  delta_trigger: 3 # Optional humidity swing to detect. Default = 3
  target_offset: 3 # Optional dehumidification target offset. Default = 3
  min_on_timer: 300 # Optional min on time in seconds. Default = 0 seconds
  max_on_timer: 7200 # Optional safety max on time in seconds. Default = 7200 seconds
  sample_interval: 300 # Optional time between taking humidity samples in seconds, default 300 seconds
  min_humidity: 30 # Optional minimum humidity to enable dehumidification. Default = 0
  unique_id: bathroom_hygrostat # This enables you to edit the device in HAS UI
```

This will create a binary sensor called `binary_sensor.bathroom_hygrostat`. Next, add some automations to switch your fan:

```yaml
automation:
- id: '1234youruniqueID1234'
  alias: Bathroom fan controler
  description: Fan control for bathroom
  mode: single
  trigger:
  - platform: state
    entity_id:
    - binary_sensor.bathroom_hygrostat
  condition: []
  action:
  - choose:
    - conditions:
      - condition: state
        entity_id: binary_sensor.bathroom_hygrostat
        state: 'on'
      sequence:
      - service: switch.turn_on
        data: {}
        target:
        entity_id: switch.bathroom_fan
    default:
      - service: switch.turn_off
        data: {}
        target:
        entity_id: switch.bathroom_fan
```

## Installation

### Manual

1. Clone this repo
1. Copy the custom_components/relative_hygrostat folder into your HA's `custom_components` folder
1. Setup the generic hygrostat custom integration as described above

