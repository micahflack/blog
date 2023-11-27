---
layout: post
title: Logitech Pro X Wireless EQ
---

Last Black Friday I nabbed some Logitech Pro X Wireless headphones from BestBuy. Which turned out to be a terrible deal since BestBuy wanted like ~$120 and Amazon had the same ones for $89 with free shipping. CamelCamelCamel shows that the price has typically hovered around what I bought them for too... so, not really much of a sale.

Impulse purchasing aside, the headset is pretty nice. I like the leather headband and the metal fork that attaches each cup. The headset even came with a spare set of ear cushions vs the default leather cups. These are a little more comfortable since they do not get as sweaty.

As for the actual sound quality of the headset, it is pretty "meh". I played around with Spotify trying out different sounds to see how the headset responds to Treble or Bass heavy songs, and the response really varies. It is also pretty hard to find songs that aren't intentionally distorted? It's as if the ongoing trend is to make songs intentionally muddied or unclear (e.g., anything Linkin Park or EDM).

Some songs that responded well though:

* Back in Black AC/DC

* Hyperballad by Bjork

* As the World Caves In by Sarah Cothran

* Thot Shit by Megan Thee Stallion

To overcome some of the imbalances, I download the EasyEffects tool. It is the successor to PulseEffects and is compatible with Pipewire (or, the pulseaudio replacement, pipewire-pulse). The software is easy on the eyes and simple. See the Github repo [here](https://github.com/JackHack96/EasyEffects-Presets){:target="_blank"}.

![EasyEffects Software](/images/EasyEffects/Example.png)

Installation is straightforward:

    sudo apt install easyeffects

Then, run the following curl command to download and install EQ presets:

    bash -c "$(curl -fsSL https://raw.githubusercontent.com/JackHack96/PulseEffects-Presets/master/install.sh)"

You can select whichever you want, but choosing (1) will install everything. Using the preset, "Perfect EQ" really balanced out some of the treble inaccuracies and added depth to the audio.

In the future, try using a system-wide EQ before giving up on a new headset.