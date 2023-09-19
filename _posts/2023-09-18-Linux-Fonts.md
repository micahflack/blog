---
layout: post
title: Unreadable Linux Fonts
---

I've been having this issue with Debian/Ubuntu based distros lately. This is probably specific to my specific version, Debain 12 KDE, but it is worth sharing. If you have unreadable font, like grey font on a white background, it's because the application you are running does not adhere to the system theme settings.

In my case, most of the VMWare applications have this problem when you use non-standard themes. Within Debian 12 KDE, I am using:

* Breeze Dark
    - global theme
    - plasma style
    - colors
* Breeze
    - application style
    - window decorations
    - cursors
* ePapirus-Dark
    - icons

And this isn't the default configuration. It looks a lot better though. To fix it you need to add an environment variable to the application launcher. Either, by changing the *.desktop file, or right clicking the app within the KDE launcher and choosing 'Edit Application'.

Then, choose the tab 'Application' and add the following to the 'Environment Variables' field.

[<img src="{{ site.baseurl }}/images/LinuxFonts/app_config.png" alt="Application Environment Vars" style="width: 400px;"/>]({{ site.baseurl }}/)

When you launch this application again it will use the default theme and all fonts/menus should be visible.

You can also modify the *.desktop file and add the above env var before the application on the 'Exec=...' line.

For example:

* Exec=vmware-view %u
* becomes...
* Exec=env GTK_THEME=breeze vmware-view %u

See this askubuntu [post](https://askubuntu.com/questions/1380141/how-do-i-reveal-hidden-obscured-text-in-vmware-horizon-client-linux).