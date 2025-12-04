---
layout: post
title: "How to Disable User Switching in GNOME 3 (in CentOS 7)"
date: 2019-09-11
last_modified_at: 2019-09-11
---

<p>For quite a while, I have wanted to disable the ability to switch users in GNOME on CentOS. Previously, I was wanting to do it for CentOS 6, but on a new system with CentOS 7, I revisited the problem today for the first time in years, and was finally able to figure out how to completely disable user switching. Here, I will first explain the overall issues and which options of the GNOME environment need to be changed, and then explain how I implemented these changes in CentOS 7.</p>

<p>For anyone trying to turn off user switching, you've probably figured out that there are actually two aspects of the interface that you want to change. First, you want to disable the menu item, visible when a user is logged in and goes to the "logout" meanu, that says "Switch users". How to do this is well document in multiple forums. One needs to set the <code>disable-user-switching</code> key to <code>true</code> in <code>org.gnome.desktop.lockdown</code> (yes, you're enabling the disabling).</p>

<p>Unfortunately, every community forum one can find that documents the above, seems to have this solution as the next-to-last post in the thread. The last post is the one that says "Okay, that disables user switching from the menu. How do you disable the "Login as a different user" option on the lock screen?" After that, there never seems to be a response.</p>

<p>I was happy to discover (using "gsettings list-recursively") the way to disable the "Login as a different user" item on the lock screen. Here, you have to set the <code>user-switch-enabled</code> key in <code>org.gnome.desktop.screensaver</code> to <code>false</code> (yes, you're disabling the enabling).</p>

<p>To do this in CentOS 7, I created the file <code>/etc/dconf/db/local.d/00-user-switching</code>. The contents of that file are:</p>

<pre>
[org/gnome/desktop/lockdown]
disable-user-switching=true

[org/gnome/desktop/screensaver]
user-switch-enabled=false
</pre>

<p>Then you can reboot, or run <code>deconf update</code>.</p>

<p>After that, both the menu item and the link on the lock screen should be gone.</p>

<dl>
  <dt>Audiobook of the Day</dt>
  <dd>Foundation by Isaac Asimov</dd>
</dl>



