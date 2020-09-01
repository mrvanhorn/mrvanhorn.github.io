---
layout: post
title: "Replacing Oracle's Java with AdoptOpenJDK and OpenWebStart on a Mac"
date: 2020-09-01
last_modified_at: 2020-09-01
---

<p>I did this on a MacPro running macOS 10.14.6 (Mojave).</p>

<p>To replace Oracle’s Java with AdoptOpenJDK and OpenWebStart, I had to run the following commands:</p>

<pre>
sudo rm -fr /Library/Internet\ Plug-Ins/JavaAppletPlugin.plugin
sudo rm -fr /Library/PreferencePanes/JavaControlPanel.prefPane
sudo rm -fr ~/Library/Application\ Support/Oracle/Java
 rm -r ~/"Library/Application Support/Oracle/Java"
sudo rm /usr/bin/javaws
</pre>

<p>Then I installed AdoptOpen’s OpenJDK 8 binaries, and OpenWebStart. I went into the OpenWebStart settings and had it find the local AdoptOpen JVM. After that, I could open a .jnlp file and it worked.</p>


<dl>
  <dt>THING of the Day</dt>
  <dd></dd>
</dl>



