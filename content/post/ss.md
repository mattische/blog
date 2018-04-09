---
title: "ss"
date: 2018-04-09T15:34:33+02:00
tags: [ "ss", "network", "terminal" ]
author: "mattische"
---

### ss
ss is a wonderful tool to monitor/vew connections on and to your machine (server).


Just running <code>ss</code> shows all connections. You may filter it by socket type (tcp, udp, unix, raw etc) and on each connections state.
To show current listening sockets use 
<code>$ ss -l</code>

<br>

If you use ss with <code>watch</code> you'll get a real time updated list of connections.

For exampel, here we show only Ipv4 tcp-connections (the -t4 switch) that are currently connected (state connected);

<code>
$ watch -n 1 "ss -r -t4 state connected"
</code>


<br>
<br>
<br>
<br>
<br>
<br>

reference:

<a href="https://www.ghacks.net/2018/04/07/using-ss-to-monitor-connections-in-gnu-linux-for-new-users/">ghacks.net</a>