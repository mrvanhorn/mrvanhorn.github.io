---
layout: post
title: "my system_info script"
date: 2026-04-03
last_modified_at: 2026-04-03 14:12:00
# to get last_modified use :r!date +"\%Y-\%m-\%d \%H:\%M:00"
---

<p>I have just created my first public GitHub repo (besides my GitHub Pages site)!</p>

<p><a href="https://github.com/mrvanhorn/system_info">https://github.com/mrvanhorn/system_info</a></p>

<p>I have uploaded my personal system_info script. This is a simple bash script that
utilizes some installed-on-pretty-much-every-Linux-distro utilities to neatly display 
some basic information about the hardware your system has. It prints the make and model
of the system (from the BIOS), information about the CPU and RAM, some info about the 
OS, and it lists all of the attached storage (block) devices.<p>

<p>The output looks like:</p>

<pre>
[user@server ~]$ sudo /usr/local/bin/system_info

Hewlett-Packard HP Z220 CMT Workstation
Serial Number: 2UA3282N1N

1 x 4-core Intel(R) Xeon(R) CPU E3-1270 V2 @ 3.50GHz
with a total of 8 threads

32GB of DDR3 RAM

NAME               MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                  8:0    0 931.5G  0 disk 
├─sda1               8:1    0  1000M  0 part /boot/efi
├─sda2               8:2    0     2G  0 part /boot
├─sda3               8:3    0   716G  0 part 
│ ├─almalinux-root 253:0    0   200G  0 lvm  /
│ ├─almalinux-swap 253:1    0    16G  0 lvm  [SWAP]
│ └─almalinux-home 253:2    0   500G  0 lvm  /home
└─sda4               8:4    0 212.5G  0 part /vmfiles
sdb                  8:16   0 931.5G  0 disk 
├─sdb1               8:17   0 931.5G  0 part 
└─sdb9               8:25   0     8M  0 part 
sdc                  8:32   0 931.5G  0 disk 
├─sdc1               8:33   0 931.5G  0 part 
└─sdc9               8:41   0     8M  0 part 
sr0                 11:0    1  1024M  0 rom  

running "AlmaLinux 9.7 (Moss Jungle Cat)"

</pre> 

<p>&nbsp;</p>

<dl>
  <dt>Record Number of Blog Posts in One Day</dt>
  <dd>2</dd>
</dl>



