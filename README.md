# Home Assistant
I created this repository to share Home Assistant related codes

# 1. Fitness Stats Comparison Card
A card to compare fitness stats using fitbit or other integrations. In this case, I used the info provided by the official HA  Fitbit integration and a custom Fitbit integration because the official HA Fitbit integration doesn't support multiple accounts. If other fitness integrations provide similar information, sensors can be modified easily.

![scoreboard](https://github.com/satyambaba/homeassistant/assets/6833101/a3d49514-13b7-44f6-81e1-8ce6f0c73efd)

***1.1 Features***
- This card displays and compares key fitness stats in a concise and organized manner to keep us motivated
- Stars are earned by comparing steps, active hours and sleep hours
- Winner is decided based on the number of stars earned
- Individual icons are colored based on sensor values. For example - Step icons are green if steps are greater than 10K, orange if steps are between 3K - 10K and red if steps are less than 3K

***1.2 Steps to use***
- Install cards mentioned in 1.4
- Create template sensors mentioned in 1.5
- Use YAML code mention in 1.3
  
***1.3 YAML Code for Card***
```yaml
type: custom:vertical-stack-in-card
style: |
  ha-card{
    background: rgba(25, 25, 25, 0.3);
    padding: 15px 0px 15px 0px;
  }
cards:
  - type: custom:stack-in-card
    style: |
      ha-card{
        background: rgba(25, 25, 25, 0);
      }
    mode: horizontal
    cards:
      - type: custom:vertical-stack-in-card
        style: |
          ha-card{
            background: rgba(25, 25, 25, 0);
            border-radius: 0px;
            border-right: 1px solid #787878;
          }
        cards:
          - type: custom:vertical-stack-in-card
            style: |
              ha-card {
                background: none;
                box-shadow: none;
              }
            cards:
              - type: custom:paper-buttons-row
                styles:
                  padding: 0px 0px 15px 0px
                buttons:
                  - entity: person.person1
                    name: >
                      {% if states('sensor.person1_fitness_score') >
                      states('sensor.person2_fitness_score') %}Person1 🏆{% else
                      %}Person1{% endif %}
                    layout: icon_name
                    image: >
                        {{state_attr('person.person1','entity_picture')}}
                    style:
                      button:
                        font-size: 1.1rem
                        font-weight: 500
                        padding: 0px 0px 0px 0px
                      icon:
                        height: 50px
                        width: 50px
                      name:
                        padding: 5px 0px 0px 0px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person1_distance
                    icon: mdi:map-marker-distance
                    name: |
                      {{states(config.entity) | round(1)}} km
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        transform: rotateY(0deg)
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person1_steps
                    icon: mdi:shoe-sneaker
                    name: >
                      {{"{:,.0f}".format(states(config.entity) | int)}} {% if
                      (states('sensor.person1_steps') | float) >
                      (states('sensor.person2_steps') | float) %}⭐{% else %}{% endif %}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        transform: rotateY(180deg)
                        color: >
                          {% if states(config.entity) | float(0) < 3000
                          %}var(--threshold-red) {% elif states(config.entity) |
                          float(0) < 10000 %}var(--threshold-orange) {% else
                          %}var(--threshold-green){% endif %}
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person1_inactive_hours
                    icon: mdi:seat-recline-normal
                    name: >
                      {% if states(config.entity) | float(0) < 1 %}
                      {{((states(config.entity)) | float * 60) | round(0)}} m {%
                      else %} {{(states(config.entity) | float(0)) | round(1)}}
                      h {% endif %}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person1_active_hours
                    icon: mdi:run-fast
                    name: >
                      {% if states(config.entity) | float(0) < 1 %}
                      {{((states(config.entity)) | float * 60) | round(0)}} m {%
                      else %} {{(states(config.entity) | float(0)) | round(1)}}
                      h {% endif %} {% if (states('sensor.person1_active_hours') |
                      float) > (states('sensor.person2_active_hours') | float)
                      %}⭐{% else %}{% endif %}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person1_sleep_efficiency
                    icon: >
                      {% if states(config.entity) | float(0) >= 60
                      %}mdi:thumb-up {% else %}mdi:thumb-down{% endif %}
                    name: |
                      {{states(config.entity)}}%
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                        transform: >
                          {% if states(config.entity) | float(0) >= 60
                          %}rotateY(0deg){% else %}rotateY(180deg){% endif %}
                        color: >
                          {% if states(config.entity) | float(0) >= 60
                          %}var(--threshold-green) {% else
                          %}var(--threshold-red){% endif %}
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person1_sleep_hours
                    icon: mdi:bed-clock
                    name: >
                      {{(states(config.entity) | float(0)) | round(1)}} h {% if
                      (states('sensor.person1_sleep_hours') | float) >
                      (states('sensor.person2_sleep_hours') | float) %}⭐{% else
                      %}{% endif %}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                        transform: rotateY(180deg)
                        color: >
                          {% if states(config.entity) | float(0) >= 7
                          %}var(--threshold-green) {% else
                          %}var(--threshold-red){% endif %}
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.versa_3_battery
                    icon: >
                      {% if states(config.entity) == 'Low' %}mdi:battery-30 {%
                      elif states(config.entity) == 'Medium' %}mdi:battery-60{%
                      elif states(config.entity) == 'High' %}mdi:battery-90{%
                      else %}mdi:battery-60{% endif %}
                    name: |
                      {{states(config.entity)[0]}}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                        color: >
                          {% if states(config.entity) == 'Low'
                          %}var(--threshold-red){% elif states(config.entity) ==
                          'Medium' %}var(--threshold-orange){% elif
                          states(config.entity) == 'High'
                          %}var(--threshold-green){% else %}{% endif %}
                      name:
                        padding: 7px 0px 0px 0px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person1_resting_heart_rate
                    icon: mdi:heart-pulse
                    name: |
                      {{states(config.entity) | replace('unknown', '.')}}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        transform: rotateY(180deg)
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 15px 0px 0px 0px
                buttons:
                  - entity: sensor.person1_scoreboard_update_time
                    name: |
                      Updated {{states(config.entity)}} ago
                    layout: name
                    style:
                      button:
                        font-size: 0.9rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
      - type: custom:vertical-stack-in-card
        style: |
          ha-card{
            background: rgba(25, 25, 25, 0);
            border-radius: 0px;
          }
        cards:
          - type: custom:vertical-stack-in-card
            style: |
              ha-card {
                background: none;
                box-shadow: none;
              }
            cards:
              - type: custom:paper-buttons-row
                styles:
                  padding: 0px 0px 15px 0px
                buttons:
                  - entity: sensor.active_room
                    name: >
                      {% if states('sensor.person1_fitness_score') <
                      states('sensor.person2_fitness_score') %}Person2 🏆{% else
                      %}Person2{% endif %}
                    layout: icon_name
                    image: >
                        {{state_attr('person.person2','entity_picture')}}
                    style:
                      button:
                        font-size: 1.1rem
                        font-weight: 500
                        padding: 0px 0px 0px 0px
                      icon:
                        height: 50px
                        width: 50px
                      name:
                        padding: 5px 0px 0px 0px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person2_steps
                    icon: mdi:shoe-sneaker
                    name: >
                      {% if (states('sensor.person1_steps') | float) <
                      (states('sensor.person2_steps') | float) %}⭐{% else %}{% endif %}
                      {{"{:,.0f}".format(states(config.entity) | int)}}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        color: >
                          {% if states(config.entity) | float(0) < 3000
                          %}var(--threshold-red) {% elif states(config.entity) |
                          float(0) < 10000 %}var(--threshold-orange) {% else
                          %}var(--threshold-green){% endif %}
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person2_distance
                    icon: mdi:map-marker-distance
                    name: |
                      {{states(config.entity) | round(1)}} km
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        transform: rotateY(180deg)
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person2_active_hours
                    icon: mdi:run-fast
                    name: >
                      {% if (states('sensor.person1_active_hours') | float) <
                      (states('sensor.person2_active_hours') | float) %}⭐{% else
                      %}{% endif %} {% if states(config.entity) | float(0) < 1
                      %} {{((states(config.entity)) | float * 60) | round(0)}} m
                      {% else %} {{(states(config.entity) | float(0) ) |
                      round(1)}} h {% endif %}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        transform: rotateY(180deg)
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person2_inactive_hours
                    icon: mdi:seat-recline-normal
                    name: >
                      {% if states(config.entity) | float(0) < 1 %}
                      {{((states(config.entity)) | float * 60) | round(0)}} m {%
                      else %} {{(states(config.entity) | float(0)) | round(1)}}
                      h {% endif %}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                        transform: rotateY(180deg)
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person2_sleep_hours
                    icon: mdi:bed-clock
                    name: >
                      {% if (states('sensor.person1_sleep_hours') | float) <
                      (states('sensor.person2_sleep_hours') | float) %}⭐{% else
                      %}{% endif %} {{(states(config.entity) | float(0)) |
                      round(1)}} h
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                        transform: rotateY(0deg)
                        color: >
                          {% if states(config.entity) | float(0) >= 7
                          %}var(--threshold-green) {% else
                          %}var(--threshold-red){% endif %}
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.person2_sleep_efficiency
                    icon: >
                      {% if states(config.entity) | float(0) >= 60
                      %}mdi:thumb-up {% else %}mdi:thumb-down{% endif %}
                    name: |
                      {{states(config.entity)}}%
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                        transform: >
                          {% if states(config.entity) | float(0) >= 60
                          %}rotateY(180deg){% else %}rotateY(0deg){% endif %}
                        color: >
                          {% if states(config.entity) | float(0) >= 60
                          %}var(--threshold-green) {% else
                          %}var(--threshold-red){% endif %}
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 10px 0px 10px 0px
                buttons:
                  - entity: sensor.person2_resting_heart_rate
                    icon: mdi:heart-pulse
                    name: |
                      {{states(config.entity) | replace('unknown', '.')}}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 27px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
                  - entity: sensor.charge_5_battery
                    icon: >
                      {% if states(config.entity) == 'Low' %}mdi:battery-30 {%
                      elif states(config.entity) == 'Medium' %}mdi:battery-60{%
                      elif states(config.entity) == 'High' %}mdi:battery-90{%
                      else %}mdi:battery-60{% endif %}
                    name: |
                      {{states(config.entity)[0]}}
                    layout: icon_name
                    style:
                      button:
                        font-size: 1rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                        color: >
                          {% if states(config.entity) == 'Low'
                          %}var(--threshold-red){% elif states(config.entity) ==
                          'Medium' %}var(--threshold-orange){% elif
                          states(config.entity) == 'High'
                          %}var(--threshold-green){% else %}{% endif %}
                      name:
                        padding: 7px 0px 0px 0px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
              - type: custom:paper-buttons-row
                styles:
                  padding: 15px 0px 0px 0px
                buttons:
                  - entity: sensor.person2_scoreboard_update_time
                    name: |
                      Updated {{states(config.entity)}} ago
                    layout: name
                    style:
                      button:
                        font-size: 0.9rem
                        padding: 0px 0px 0px 0px
                      icon:
                        '--mdc-icon-size': 22px
                    tap_action:
                      action: none
                    hold_action:
                      action: none
```
***1.4 Requirements***
- [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card)
- [Paper Buttons Row Card](https://github.com/jcwillox/lovelace-paper-buttons-row)
- [Card Mod](https://github.com/thomasloven/lovelace-card-mod)

***1.5 Details about Sensors***

- Sensors Provided by the official HA Fitbit Integration which are used (directly or in a template sensor) in this card - Steps, Distance, Minutes Fairly Active, Minutes Lightly Active, Minutes Very Active, Minutes Sedentary, Sleep Efficiency, Minutes Asleep, Resting Heart Rate and Battery
  
- Sleep Hours - Template Sensor to calculate sleep hours
  
  ```yaml
    person1_sleep_hours:
      friendly_name: "Person1 Sleep Hours"
      value_template: >
        {{(states('sensor.person1_sleep_minutes_asleep') | float(0) / 60) | round(1)}}
  ```
  Please create the same sensor for person2.

- Active Hours - Template Sensor to calculate active hours
  
  ```yaml
    person1_active_hours:
      friendly_name: "Person1 Active Hours"
      value_template: >
        {{((states('sensor.person1_minutes_lightly_active') | float(0) + states('sensor.person1_minutes_fairly_active') | float(0) + states('sensor.person1_minutes_very_active') | float(0)) / 60) | round(1)}}
  ```
  Please create the same sensor for person2.

- Inactive Hours - Template Sensor to calculate inactive hours
  
  ```yaml
    person1_inactive_hours:
      friendly_name: "Person1 Inactive Hours"
      value_template: >
        {{(states('sensor.person1_minutes_sedentary') | float(0) / 60) | round(1)}}
  ```
  Please create the same sensor for person2.

- Active Hours Percentage - Template Sensor to calculate active hours percentage
  
  ```yaml
    person1_active_hours_percentage:
      friendly_name: "Person1 Active Hours Percentage"
      device_class: "battery"
      unit_of_measurement: "%"
      value_template: >
        {{(states('sensor.person1_active_hours') | float * 100 / (states('sensor.person1_active_hours') | float + states('sensor.person1_inactive_hours') | float)) | round(0) }}
  ```
  Please create the same sensor for person2.

- Fitness Wins - Template Sensor to store "wins" info based on comparing steps, active hours and sleep hours

  ```yaml
    person1_fitness_wins:
      friendly_name: "Person1 Fitness Wins"
      value_template: >
        {% if (states('sensor.person1_steps') | float) >
        (states('sensor.person2_steps') | float) %}1{% else %}0{% endif %}{%
        if (states('sensor.person1_active_hours') | float) >
        (states('sensor.person2_active_hours') | float) %}1{% else %}0{% endif %}{% if
        (states('sensor.person1_sleep_hours') | float) >
        (states('sensor.person2_sleep_hours') | float) %}1{% else %}0{% endif %}
    ```
    Please create the same sensor for person2.

- Fitness Score - Template Sensor to calculate score based on wins.
  
  ```yaml
      person1_fitness_score:
      friendly_name: "Person1 Fitness Score"
      value_template: >
        {{((states('sensor.person1_fitness_wins')[0] | int) + (states('sensor.person1_fitness_wins')[1] | int) + (states('sensor.person1_fitness_wins')[2] | int)) | int}}
  ```
  Please create the same sensor for person2.

  - Scoreboard Update Time - Template Sensor to calculate the scoreboard last update time.
  
  ```yaml
    person1_scoreboard_update_time:
      friendly_name: Person1 Scoreboard Update Time
      value_template: >
        {% if ((as_timestamp(now())-max(as_timestamp(states.sensor.person1_steps.last_updated),as_timestamp(states.sensor.person1_resting_heart_rate.last_updated),as_timestamp(states.sensor.person1_sleep_minutes_asleep.last_updated),as_timestamp(states.sensor.versa_3_battery.last_updated)))/3600) | float > 1 %}{{(((as_timestamp(now())-max(as_timestamp(states.sensor.person1_steps.last_updated),as_timestamp(states.sensor.person1_resting_heart_rate.last_updated),as_timestamp(states.sensor.person1_sleep_minutes_asleep.last_updated),as_timestamp(states.sensor.versa_3_battery.last_updated)))/3600) | int)}}h
        {% else %}{% endif %}{{(((((as_timestamp(now())-max(as_timestamp(states.person1_fitbit_steps.last_updated),as_timestamp(states.sensor.person1_resting_heart_rate.last_updated),as_timestamp(states.sensor.person1_sleep_minutes_asleep.last_updated),as_timestamp(states.sensor.versa_3_battery.last_updated)))/3600) | float)-(((as_timestamp(now())-max(as_timestamp(states.sensor.person1_steps.last_updated),as_timestamp(states.sensor.person1_resting_heart_rate.last_updated),as_timestamp(states.sensor.person1_sleep_minutes_asleep.last_updated),as_timestamp(states.sensor.versa_3_battery.last_updated)))/3600) | int))*60) | round(0)}}m

  ```
  Please create the same sensor for person2.
