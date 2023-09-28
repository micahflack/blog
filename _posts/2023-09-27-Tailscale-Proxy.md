---
layout: post
title: Setup Tailscale for Proxy Use
---

Sometimes you need to use Tailscale, but all traffic for the network you're on needs to passthrough a proxy. There is some conflicting information about this on the wiki and issues pages for Tailscale on Github. See this issue [#931](https://github.com/tailscale/tailscale/issues/931#issuecomment-728195103).

This can be fixed by adding the following lines to `/etc/default/tailscaled`. IF this doesn't exist for you, check the systemd file at `/lib/systemd/system/tailscaled.service`.

    # Set the port to listen on for incoming VPN packets.
    # Remote nodes will automatically be informed about the new port number,
    # but you might want to configure this in order to set external firewall
    # settings.
    PORT="41641"

    HTTPS_PROXY="http://YOUR_ADDRESS:PORT"

    # Extra flags you might want to pass to tailscaled.
    FLAGS=""

You can try using a username and password, but you will eventually run into formatting errors if the strings aren't encoded correctly becuase of special characters. If the proxy I am being forced into using requires them, I will setup a local proxy forwarder that doesn't require a user and pass.

    Tailscale --> No auth local proxy --> Auth'd proxy

For this, you can either use the python package pproxy or setup local squid/dante proxy forwarders.