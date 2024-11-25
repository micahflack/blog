---
layout: post
title: Google Timeline Alternatives and Tracker Updates
---

Despite the privacy concerns that come with Google Timeline, I personally like the tool because of how easy it is to see where I've been through time and what I was doing. It's like a passive journal that I don't need to commit to updating every day. However, that will be going away completely as of December 1st, 2024: [Google Maps Timeline now stores your location data on-device](https://www.androidpolice.com/google-maps-timeline-location-data-on-device-migration/).

It used to be that the Timeline was accessible from... http://timeline.google.com/

But, that was disabled earlier this year too. Now where does that leave us and what alternatives are available? From what I've gathered, these are the best self-hosted options:

- [Dawarich](https://dawarich.app/)

- [OwnTracks](https://owntracks.org/) (Client/Server)

- Nextcloud + [Phonetrack](https://apps.nextcloud.com/apps/phonetrack) (Server)

- [Home Assistant](https://www.home-assistant.io/) (Client)

Sometimes you will them referred to as agents or recorders. The agents run on the device being tracked and report their location to the recorder service. Not all recorder services also have a Web UI for viewing (i.e., OwnTracks), so that is something else to consider.

If you would like to explore those options, I recommend these articles:

- [Github - Converting Google Timeline Data to GPX](https://gist.github.com/lachiefish/5837c0ec263aa956acc92963d9ef0d8e)

- [Github - Dawarich](https://github.com/Freika/dawarich)

- [Self-Hosted Location History - Leaving Google](https://blog.tiga.tech/posts/selfhost-location-history/)

- [muetsch.io - Self Hosted Location Tracking with OwnTracks and Grafana](https://muetsch.io/open-source-self-hosted-location-tracking-with-owntracks-and-grafana.html)

Personally, I already use Home Assistant for my own local automation as well as Nextcloud - so the combo was obvious for me. If you need a way to connect to your services without exposing them, I also recommend using Tailscale or running a local Wiregard tunnel; pick whatever best fits your uses.

This is everything I needed to connect my HomeAssistant location sensors to the Phonetrack recorder:

```
# This is how you add shell script commands to HomeAssistant.
# Add the section under your /config/configuration.yaml
shell_command:
  phonetrack_curl: "/bin/bash /config/scripts/phonetrack.sh {{ args }}"
```

This is a local script kept under the /config/scripts directory of HomeAssistant. It is triggered by the shell_command in the earlier block, "phonetrack_curl"

```
#!/bin/bash

LAT="$1"
LON="$2"
ALT="$3"
ACC="$4"
BAT="$5"
SAT="$6"
SPD="$7"
DIR="$8"
TIME="$9"

/usr/bin/curl -G --ipv4 "https://nextcloud.caddy.lan/apps/phonetrack/logGet/SECRET/Micah%20OPO?" \
--data-urlencode "lat=$LAT" \
--data-urlencode "lon=$LON" \
--data-urlencode "alt=$ALT" \
--data-urlencode "acc=$ACC" \
--data-urlencode "bat=$BAT" \
--data-urlencode "sat=1" \
--data-urlencode "speed=$SPD" \
--data-urlencode "bearing=$DIR" \
--data-urlencode "timestamp=$TIME"
```

This is the HomeAssistant automation for reporting the sensor data from my phone "device_tracker.oneplus_open" to phonetrack.

```
alias: "[Location] Update Phonetrack"
description: "Updates the Nextcloud phonetrack service with current location "
trigger:
  - platform: state
    entity_id:
      - device_tracker.oneplus_open
condition: []
action:
  - action: shell_command.phonetrack_curl
    data_template:
      args: >-
        {{ states('sensor.opo_latitude') }} {{ states('sensor.opo_longitude') }}
        {{ states('sensor.opo_altitude') }} {{ states('sensor.opo_accuracy') }}
        {{ states('sensor.oneplus_open_battery_level') }} 0 {{
        states('sensor.opo_speed') }} {{ states('sensor.opo_course') }} {{
        now().timestamp() }}
mode: single
```

The helper values I created to support the HomeAssistant automation are as follows:

- opo_latitude

- opo_longitude

- opo_altitude

- opo_accuracy

- opo_speed

- opo_course

Each of these refers to a part of the data required for Phonetrack to map your device.

The sensor template for the helpers follows as such:

```
{{ state_attr('device_tracker.oneplus_open','gps_accuracy') }}
```

But replace “gps_accuracy” with whichever part you are making a sensor for (e.g., lat, lon, alt, acc, speed, course, etc….)

I also created an “input_text.ntfy_location” helper for tracking updates sent to my NTFY service. I used to use Tasker on my phone to control my cubicle tracker, however, I found that it was unreliable and HomeAssistant is far more capable. Tasker is only used by my phone for on-device automation. If it requires something with extra processing and external connections, I merely send sensor updates to HA and let it process whatever is needed instead.

![HomeAssistant](/images/phonetrack/ha.png)

If everything is working between your phone, running HA companion app, the HA server, and your Nextcloud service with Phonetrack, you should see something like the following:

![Phonetrack](/images/phonetrack/maps.png)

Compared to what was lost with the dashboard for Google Timeline being removed, I think this is an adequate solution. It is significantly more to manage compared to a free service and you will be fixing whatever incompatibilities arise, but it was fun learning how to make everything work together. For those without existing HomeAssisntant and Nextcloud services, I recommend sticking with Dawarich and OwnTracks. 

Continue reading to see how I control my cubicle location tracker….

## Replacing Tasker with Home Assistant

All of the HA automations needed to replace Tasker are as such:

- [Location] Entering Work Zone

- [Location] Leaving Work Zone

- [Location] Update Cubicle Tracker - EOD

- [Location] Update Cubicle Tracker - Remote

- [Location] Update NTFY Cubicle Tracker

The “[Location]” part of the name is a sort of tagging system that I used to organize my automations by name. It just helps make it easier to scroll through the view in alphabetic view and find what I need - so, name it whatever you want, it isn't important.

### [Location] Entering Work Zone

This checks my sensor.opo_zone to see if my device has entered a zone containing the word “WORK” in the label. If it has, the automation knows to update my input_text.ntfy_location helper with the zone label. For instance, am I at “WORK A”, “WORK B”, “WORK C”.

```
alias: "[Location] Entering Work Zone"
description: ""
trigger:
  - platform: state
    entity_id:
      - sensor.opo_zone
condition:
  - condition: template
    value_template: |-
      {% if 'WORK' in states('sensor.opo_zone') %}
          true
      {% else %}
          false
      {% endif %}
action:
  - action: input_text.set_value
    metadata: {}
    data:
      value: "{{ states('sensor.opo_zone') }}"
    target:
      entity_id: input_text.ntfy_location
mode: single
```

### [Location] Leaving Work Zone

Similar concept to the “Entering Work Zone” automation, it checks to see if the new zone does not contain the word “WORK” in the label. If it does not, the input_text.ntfy_location helper is changed to “Out-Of-Office”.

```
alias: "[Location] Leaving Work Zone"
description: ""
trigger:
  - platform: state
    entity_id:
      - sensor.opo_zone
condition:
  - condition: template
    value_template: >-
      {% if 'WORK' in states('input_text.ntfy_location') and 'WORK' not in
      states('sensor.opo_latitude') %}
          true
      {% else %}
          false
      {% endif %}
action:
  - action: input_text.set_value
    metadata: {}
    data:
      value: Out-Of-Office
    target:
      entity_id: input_text.ntfy_location
mode: single
```

### [Location] Update Cubicle Tracker - EOD

This just checks that the time is 6PM, a weekday, and the current location is “Remote”. If so, it changes the current location to “Out-Of-Office”.

```
alias: "[Location] Update Cubicle Tracker - EOD"
description: ""
trigger:
  - platform: time
    at: "18:00:00"
condition:
  - condition: time
    weekday:
      - mon
      - tue
      - wed
      - thu
      - fri
  - condition: state
    entity_id: input_text.ntfy_location
    state: Remote
action:
  - action: input_text.set_value
    metadata: {}
    data:
      value: Out-Of-Office
    target:
      entity_id: input_text.ntfy_location
mode: single
```

### [Location] Update Cubicle Tracker - Remote

This just checks that the time is 8AM, a weekday, and the current location is “Out-Of-Office”. If so, it changes the current location to "Remote”.

```
alias: "[Location] Update Cubicle Tracker - Remote"
description: ""
trigger:
  - platform: time
    at: "08:00:00"
condition:
  - condition: time
    weekday:
      - mon
      - tue
      - wed
      - thu
      - fri
  - condition: state
    entity_id: input_text.ntfy_location
    state: Out-Of-Office
action:
  - action: input_text.set_value
    metadata: {}
    data:
      value: Remote
    target:
      entity_id: input_text.ntfy_location
mode: single
```

### [Location] Update NTFY Cubicle Tracker

Whenever the contents of input_text.ntfy_location changes, push the contents to the NTFY server and topic. This is also limited to weekdays only - no need to update on weekends.

```
alias: "[Location] Update NTFY Cubicle Tracker"
description: ""
trigger:
  - platform: state
    entity_id:
      - input_text.ntfy_location
condition:
  - condition: time
    weekday:
      - mon
      - tue
      - wed
      - thu
      - fri
action:
  - action: shell_command.ntfy_curl
    metadata: {}
    data_template:
      args: test {{ states('input_text.ntfy_location') }}
mode: single
```

### shell_command.ntfy_curl

This is the entry I used to add the command to my /config/configuration.yaml

```
shell_command:
  ntfy_curl: "/bin/bash /config/scripts/ntfy.sh {{ args }}"
```

### ntfy.sh

And this is the contents of my ntfy script triggered by the shell command called “ntfy_curl”

```
#!/bin/bash

TOPIC="$1"
MESSAGE="$2 $3"

/bin/echo "$TOPIC $MESSAGE" > /config/scripts/curl.log

/usr/bin/curl -d "$MESSAGE" "https://ntfy.YOUR_DOMAIN.com/$TOPIC"
```
