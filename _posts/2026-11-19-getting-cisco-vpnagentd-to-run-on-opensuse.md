---
layout: post
title: "What I Had To Do to Get Cisco vpnagentd to Start Under openSUSE 16.0"
date: 2026-11-19
last_modified_at:
# to get last_modified use :r!date +"\%Y-\%m-\%d \%H:\%M:00"
---

<p>&nbsp;</p>

<p>
In order for the Cisco Secure Client vpn client to run on Linux, there is a daemon that runs in the
background called vpnagentd. There is a systemd service file for that daemon, which on my laptop running
openSUSE 16.0, is /etc/systemd/system/vpnagentd.service. When the service would try to start, it would fail
with an unhelpful error message "resources" as the reason. Looking in the journal 
(<code>journalctl -xeu vpnagentd.service</code>), the following errors were present
</p>

<pre>
Nov 18 21:31:52 mysystem systemd[1]: vpnagentd.service: Failed to load environment files: No such file or directory
Nov 18 21:31:52 mysystem systemd[1]: vpnagentd.service: Failed to spawn 'start-pre' task: No such file or directory
Nov 18 21:31:52 mysystem systemd[1]: vpnagentd.service: Failed with result 'resources'.
</pre>

<p>
The part about the "environment files" is the key thing. In the .service file, there is a line that read
</p>

<pre>
EnvironmentFile=/etc/environment
</pre>

<p>
My system didn't have that file, so I commented out that line in the service file. Thus, it now looks like
</p>

<pre>
#EnvironmentFile=/etc/environment
</pre>

<p>
I haven't tried it, but I believe I could have also
</p>

<pre>
touch /etc/environment
</pre>

<p>
and that would have worked, too. Having that file be empty shouldn't have broken anything. 
</p>

<p>
Once that line was commented out, the vpnagentd process starts with no problems and I can connect the client.
</p>

