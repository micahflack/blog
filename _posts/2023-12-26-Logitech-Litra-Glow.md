---
layout: post
title: Logitech Litra Glow w/ Linux
---

Merry Christmas! 🎅🎄

I am a newfound owner of a Logitech Litra Glow, a monitor/desk mountable backlight used for video calls or streaming. The lamp itself is pretty handy and can move between a yellow to white warmth (2700-6500k). And, it is connected via a USB-C to USB-A cable which delivers data and power to the lamp.

![Litra](/images/LogitechLitra/litra.webp)

As handy as the device is, the controls are a little lack luster without the accompanying logitech g-suite software (Windows only). However I recently came across this Github issue showing that the lamp can be controlled using HID API calls. [Github: Get tools working on mac without sudo #13 ](https://github.com/kharyam/litra-driver/issues/13){:target="_blank"}.

Mainly, the first two commands that were identified were for toggling the lamp.

Light On:

     hidapitester --vidpid 046D/C900 --open --length 20 --send-output 0x11,0xff,0x04,0x1c,0x01

Light Off:

     hidapitester --vidpid 046D/C900 --open --length 20 --send-output 0x11,0xff,0x04,0x1c

To make this easier, I have both of these commands mapped to global shortcuts via my keyboard.

To turn the light on...

    WIN + ALT + F11

And, to turn the light off...

    WIN + ALT + F12

It isn't the most graceful solution, but it gets the job done. I think you could write some scripts that just quickly step through each HID API call to mimic "profiles". Like, you have a profile that has 2700k warmth and minimal brightness. So, you spam the minimize brightness API call for the full range. And then you do the same again to reach the predefined warmth/temperature.

I haven't done that yet, it's just an idea 🤷

![Shortcut](/images/LogitechLitra/shortcut_setting.png)

