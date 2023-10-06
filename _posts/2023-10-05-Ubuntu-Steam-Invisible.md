---
layout: post
title: Steam Invisible on Ubuntu?
---

For awhile I've been running a valheim server for my friends and I using a docker container. This doesn't really work well though if you plan on adding mods to the server outside of just BepInEx and ValheimPlus. So, I tried making the shift to an Ubuntu desktop server instead.

Normally, this would be a pretty smooth process - but, I ran into an error I haven't seen before with Steam and Linux. After installing the steam-installer package through apt, as well as the official .deb package from the Steam site, the main window would appear invisible at boot. For reference, I ran into this problem on Ubuntu 20.04.6 LTS and Steam version 1696019606.

Luckily, the fix is pretty simple. I found this comment on a [Reddit post](https://old.reddit.com/r/linuxmint/comments/155d5b1/steam_is_invisible/) describing this exact problem...

> I just had the same issue with linux mint on a vm and managed to fix it by launching steam in the terminal using "steam -cef-disable-gpu"

To make this even easier/permanent... you can modify the steam.desktop file.

    /usr/share/applications/steam.desktop

Look for the following line "Exec=..." and make these changes:

```
...
Exec=/usr/bin/steam -cef-disable-gpu %U 
...
```