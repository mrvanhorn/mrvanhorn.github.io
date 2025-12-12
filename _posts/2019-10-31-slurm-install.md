---
layout: post
title: "Step-by-step slurm installation"
date: 2019-10-31
last_modified_at: 2019-10-31
---

<p>
I recently switched an HPC cluster from torque to slurm. It went much more smoothly than
I anticipated it would, considering it's running an older distro of Linux (see below).
</p>

<p>
Credit where it's due, I got much of this from <a
href="https://www.strike3d.it/index.php/en/2018/11/06/slurm-on-centos-6/">https://www.strike3d.it/index.php/en/2018/11/06/slurm-on-centos-6/</a>.
It is a great step-by-step guide, but there were a few spots where it
wasn't clear to me if something needed to be done on the headnode or
the compute nodes, so I've clarified that where I needed to, and I also
added the inclusion of the pmix library.
</p>

<p>
In this situation, I am installing slurm on a cluster where the headnode and the
compute nodes are all running CentOS 6.10. This cluster is configured such that
there is no authentication service (i.e., nis, ldap, etc.) so the password,
shadow, and group files all need to be synced.
</p>

<p>
Remember that the uids and gids of the munge and slurm users must be consistent
between the headnode and the compute nodes.
</p>

<pre>
- Create slurm user on the headnode
export SLURMUSER=990
groupadd -g $SLURMUSER slurm
useradd -m -c "SLURM workload manager" -d /var/lib/slurm -u $SLURMUSER -g slurm -s /bin/bash slurm

- Create the munge user on the headnode
export MUNGEUSER=991
groupadd -g $MUNGEUSER munge
useradd -m -c "MUNGE Uid 'N' " -d /var/lib/munge -u $MUNGEUSER -g munge -s /sbin/nologin munge

- copy /etc/passwd, /etc/shadow, and /etc/group files to the compute nodes

- install epel-release
yum install epel-release

- install munge from epel
yum install munge munge-libs munge-devel

- install rng-tools
yum install rng-tools
rngd -r /dev/urandom

- generate random munge key on headnode
/usr/sbin/create-munge-key -r
dd if=/dev/urandom bs=1 count=1024 > /etc/munge/munge.key 
chown munge:munge /etc/munge/munge.key
chmod 400 /etc/munge/munge.key

- copy the munge key to compute nodes; make sure to chown and chmod the key on the compute nodes

- start munge on headnode and compute nodes
service munge start 
chkconfig munge on

- check that munge is running and working; these should work on the headnode and the compute nodes
munge -n 
munge -n | unmunge 
remunge

- download and build pmix (will be needed by slurm and openmpi) on the headnode
./configure --prefix=/usr/local/pmix-3.1.4
make
make install

- copy /usr/local/pmix-3.1.4 to all the compute nodes

- download and build openmpi (the OpenMPI that is in the CentOS 6 repos is old)
./configure --prefix=/usr/local/openmpi-4.0.2 --with-slurm --with-pmi=/usr/local/pmix-3.1.4
make
make install

- copy /usr/local/openmpi-4.0.2 to the compute nodes

- set up the PATH, so that the /usr/local/openmpi-4.0.2 files will be found
before any other MPI installations you have, on both the headnode and compute
nodes. I did this with files in /etc/profile.d. I also uninstalled the openmpi-*
packages from the CentOS repo.

- install slurm package requirements on the headnode and compute nodes; the -*devel packages aren't needed on the compute nodes
yum install openssl openssl-devel pam-devel numactl numactl-devel hwloc hwloc-devel lua lua-devel readline-devel rrdtool-devel ncurses-devel man2html libibmad libibumad

- install Slurm
./configure --prefix=/usr/local/slurm-19.05.3-2 --with-pmix=/usr/local/pmix-3.1.4
make
make install

- create slurm directory structure

on headnode and compute nodes:
mkdir /etc/slurm 
touch /var/log/slurm.log 
touch /var/log/SlurmctldLogFile.log 
touch /var/log/SlurmdLogFile.log 

on headnode:
mkdir /var/spool/slurmctld 
chown slurm:slurm /var/spool/slurmctld 
chmod 755 /var/spool/slurmctld 
touch /var/log/slurmctld.log
chown slurm:slurm /var/log/slurmctld.log
touch /var/log/slurm_jobacct.log
chown slurm:slurm /var/log/slurm_jobacct.log

on compute nodes:
mkdir /var/spool/slurmld 
chown slurm:slurm /var/spool/slurmd
chmod 755 /var/spool/slurmd

- create slurm.conf 
use https://slurm.schedmd.com/configurator.easy.html to generate slurm.conf file
make it /etc/slurm/slurm.conf

- copy configuration to the compute nodes

- configuration test (should work on both headnode and compute nodes)
slurmd -C

- create link to file (both headnode and compute nodes)
ln -s /etc/slurm/slurm.conf /usr/local/etc/slurm.conf

- On Centos 6, also set "ProcessUnpackaged = yes" in the file /etc/abrt/abrt-action-save-package-data.conf.

- To start the daemons, on both the headnode and the compute nodes, you do 
service slurm start
chkconcig slurm on

This will start slurmctld on the headnode and slurmd on the compute nodes (it figures out from the slurm.conf file)

- verify configuration
sinfo

- install cgroup
yum install libcgroup 
service cgconfig start
chkconfig cgconfig on

- vi /etc/slurm/cgroups.conf

CgroupMountpoint="/cgroup"
CgroupAutomount=yes
CgroupReleaseAgentDir="/etc/slurm/cgroup"
AllowedDevicesFile="/etc/slurm/cgroup_allowed_devices_file.conf"
ConstrainCores=yes
TaskAffinity=no
ConstrainRAMSpace=yes
ConstrainSwapSpace=yes
ConstrainDevices=no
AllowedRamSpace=100
AllowedSwapSpace=0
MaxRAMPercent=100
MaxSwapPercent=100
MinRAMSpace=30

- vi /etc/slurm/cgroup_allowed_device_file.conf

cgroup_allowed_devices_file.conf

/dev/null
/dev/urandom
/dev/zero
/dev/sda*
/dev/cpu//
/dev/pts/*

- make some links for the cgroup config files
ln -s /etc/slurm/cgroup.conf /usr/local/etc/cgroup.conf
ln -s /etc/slurm/cgroup_allowed_devices_file.conf /usr/local/etc/cgroup_allowed_devices_file.conf

- verify queue
squeue
</pre>

<dl>
  <dt>New Album of the Day</dt>
  <dd><a href="https://www.arminvanbuuren.com/2019/10/25/armin-van-buuren-teams-up-with-renowned-street-artists-for-release-of-seventh-artist-album-balance/">Balance</a> by Armin van Buuren</dd>
</dl>
