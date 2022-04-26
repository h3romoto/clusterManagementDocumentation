# Mirror Guide

This refers to the server mirror.cs.stolaf.edu which is a virtual machine on Thing 5, and is used to download Linux (Ubuntu, Centos, and Arch) images at 10 gig speed. It can be used by setting the mirror source to mirror.cs.stolaf.edu instead of something like http://us.archive.ubuntu.com/ubuntu/

# How Mirror Works

On the virtual machine, the file `/etc/crontab` contains 3 cron jobs (one per linux distro), and each of these cron jobs will run their respective shell script. These shell scripts are located in `/opt/mirror/ubuntu` `/opt/mirror/centos` and `/opt/mirror/arch`. Each script uses an rsync command to ensure that the version of the distro on the thing5 virtual machine is the same as the official version of the distro. It is essentially just checking that the contents of a directory on the VM are the same as the official files it finds on the web, and updating if necessary.

These directories are actually located in NFS mounts from the NFS server on Atlas. On the mirror virtual machine, they are located at `/mnt/mirrors/ubuntu` `/mnt/mirrors/arch` and `/mnt/mirrors/centos` and they are mounted from `162.210.90.4:srv/raid5/ubuntu-mirror` `162.210.90.4:srv/raid5/arch-mirror` and `162.210.90.4:srv/raid5/centos-mirror`  (note that this info can be gained by running df -h) Also note that the ubuntu NFS mount is much larger than the other ones, Ubuntu just provides more versions and updates than the others. 

As for how these files are served, there is an apache server running on the thing5 VM which has its webroot as `/mnt/mirrors` on the VM, and this can be seen in a browser by going to mirror.cs.stolaf.edu

# Possible Issues With Mirror

The virtual machine that was running mirror.cs.stolaf.edu only had 15GB of disk space (the actual distro files were much larger but on NFS mounts) and this 15GB ran out. A good amount of these 15 GB were taken up by old linux header files for the VM itself. To prevent this in the futute, a cron job was added that periodically runs apt --purge autoremove that will clean up these files as old ones accumulate.