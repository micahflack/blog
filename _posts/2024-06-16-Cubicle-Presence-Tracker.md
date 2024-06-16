---
layout: post
title: Presence Tracker for Cubicles
---

I've been participating in a silent arms-race with my cubicle neighbor at work lately. At first, it was just the lights and clean arrangement of office furniture (e.g, desks, cabinets, whatever...), but now he has a fancy whiteboard for updating his work 'status' or location. It shows the basics, like: out-for-lunch, out-of-office, vacation/PL, which building he's working at, etc...

To give myself an edge in this ongoing battle, I've created my own digital tracker w/ automated processes for updating my current presence/status. Using NTFY, the board can be updated from anywhere and seen from anywhere (assuming you have access 😉). This allows the board to be smoothly updated and require little to no intervention on my part.

Required parts/resources:
- ForHelp Portable Monitor - [Amazon](https://www.amazon.com/dp/B0BNWWF32B?psc=1)
- Computer w/ USB-C or HDMI
- NTFY server (can use public ntfy.sh)
- Android Tasker

If you need a longer USB-C cable than what was provided, I can recommend the following:
- Cable Matters 20Gbps USB-C to USB-C 6ft - [Amazon](https://www.amazon.com/dp/B07X31FG6Z?psc=1&ref=ppx_yo2ov_dt_b_product_details)

The following two scripts and chromium browser must run from the connected laptop:
- Flask webserver (generates page with status content)
- NTFY Websocket client (uses python to monitor NTFY topic for msgs)
- chromium window less client (displays Flask page w/ current status)

###### NOTE: I recommend running this all within a Tmux session.

Script #1 - Flask Webserver

```
#!/usr/bin/env python

from flask import Flask

app = Flask(__name__)

import asyncio
import os

@app.route("/")
def hello_world():
    f = open("sample", "r")
    msg = f.readline()
    header = '<meta http-equiv="refresh" content="30">'
    start = '<p style="font-size:50px;font-family:Console, Lucida, Monospace;">'
    end = '</p>'

    return header + start + msg + end

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=8888)
```

Script #2 - NTFY Websocket Client

```
#!/usr/bin/env python

import asyncio
import websockets
import json
import os

async def connect():
    async with websockets.connect(
        "wss://ntfy.sh:443/YOUR_SECRET_TOPIC/ws",
    ) as ws:

        print("Connected to the switch.")

        while True:

            response = await ws.recv()
            response = json.loads(response)

            os.system('clear')

            if response["event"] == "message":
                print(response["message"])
                f = open("sample", "w")
                f.write(response["message"])
                f.close()
            else:
                print("Response from the server:", response)


if __name__ == "__main__":
    asyncio.run(connect())
```

Chromium Browser

```
chromium-browser --app=http://0.0.0.0:8888/
```

To make this truly cubicle compatible, I have some 3D printable adapters that can be used with VESA 75x75 equipped monitors.

I have not tested the load capacity of these adapters, so, PLEASE do not use this with anything other than the recommended monitor.

When attaching the monitor mount, I used a TV/monitor mounting kit from Home Depot. Any flat head bolt that fits the VESA specification should be compatible with the adapter plate.

###### NOTE: the hanger is limited to 3.5" thick cubicle walls - measure yours first

[VESA 75x75 Mount for Cubicles](/docs/3d-models/75-RecessedMonitorMount.stl)

![Picture of Monitor Mount](/images/CubicleTracker/IMG20240609162853.jpg)
![Picture of Monitor Mount](/images/CubicleTracker/IMG20240609162924.jpg)

This is how the monitor should look when mounted:

![Picture of Monitor Mounted to Cubicle Wall](/images/CubicleTracker/IMG20240611094418.jpg)

To send updates to Android, I recommend using Tasker or some kind of equivalent automation application.

Here is a list of the automations that I am currently running for this tracker board:

![Picture of Tasker Automations](/images/CubicleTracker/TaskerList.jpg)

Each of the tasks follows a similar recipe:

![Picture of Task Actions](/images/CubicleTracker/TaskActions.jpg)

When the location is triggered (being within 30m of the building), it will update the NTFYLocation variable with the appropriate text and then fire off the HTTP request action.

The options used for the HTTP request are as follows:

![Picture of Task Request Options](/images/CubicleTracker/TaskActions.jpg)

For anyone using the public NTFY.sh service, those variables would look like this:
- `%NTFYServer`: https://ntfy.sh/
- `%NTFYTopic`: YOUR_SECRET_TOPIC

If you are gonna use the public NTFY servers, the creator recommends using a secret topic that anyone would have a hard time guessing. These servers do not utilize the ACL permission feature, whereas, a self-hosted server could.

I would use the password generator of your password manager to create a topic and be careful not to publicize it.

Some examples using Bitwarden (I am not using these, and you shouldn't either):
- PhV4SnP4uMEQQMdjuBsJs3xcaRzukP8BXbK4652LVGrLd8MbNoXv3rLzGBxdxQE2 (alphanumeric)
- szYwkkzSzpHRKcmTVnpbAJDBXjUynPpMDAbLJiLUGHEzYcvRQJkzyYpCoTNyxgtM (alphabet only)
- xpiybsdkhkpzhyxwuhuozzdnjuwfxyeqeqpkexestufocxrkyqqwzobiobockdys (lowercase only)

Topics have a max length of 64 bytes. I believe the data encoding used for topics affects this too. So, if you're using topics with characters not limited to a-zA-Z0-9, you could exceed 1 byte per character and experience issues.