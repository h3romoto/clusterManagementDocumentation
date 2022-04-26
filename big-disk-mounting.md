# Adding a Volume to Big Disk and Mounting on a Remote Machine

Note: Make sure both the machines are on the 10G CS network

## Setup on Big Disk

Big disk has 2 volume groups: `raid5` and `raid10`.
`raid5` is used in most cases, giving us a good amount of reliability without compromising too much on space.
`raid10` is used when the data is important and needs the extra reliability given by having two copies of the data.

[More details on RAID levels](https://www.prepressure.com/library/technology/raid)

### Create a Logical Volume

```
lvcreate -L 16G -n test-volume raid5-vg
```

This creates a 16G logical volume called `test-volume` on the `raid5-vg` (`raid10-vg` for `raid10`)
The volume group is created in `/dev/mapper/` as `dev/mapper/raid5--vg-test--volume`

### Format Volume with Proper Filesystem

```
mkfs.ext4 /dev/mapper/raid5--vg-test--volume
```

This initializes the volume with an ext4 filesystem.
Note that in `/dev/mapper`, `-` gets replaced with `--` (`raid5-vg` → `raid5--vg`, `test-volume` → `test--volume`)

### Add the Logical Volume in fstab

This will auto-mount the volume on startup.

- Edit `/etc/fstab`
- Add the entry for the disk

Here’s a sample: (first line is the comment, line starts from UUID)

```
# 2017-08-28 (sharma): ea98be40-e8cf-4963-82e9-5298c35db460 ~> /srv/raid5/cumulus-head-backup (LVM vg="raid5-vg" lv="test-volume")  
UUID=ea98be40-e8cf-4963-82e9-5298c35db460    /srv/raid5/test-volume	ext4  defaults    0     0
```

Here `/srv/raid5/test-volume` is the mount-point (folder where the volume is mounted).
**It has to be created using `mkdir` first.**

Here’s how you can obtain the UUID:
Use `ls -la` on `/dev/mapper` and get a `/dm-x` value for the volume
Then use `ls -la` on `/dev/disk/by-uuid` and the UUID that matched the dm-x value is what you need.

### Reload the Mounts from fstab

```
mount -a
```

### Add Mount Point to exports File
Now, add the mount-point to exports file to make it accessible by the remote device:

- Edit `/etc/exports`
- Add a line that looks like following (this one makes it available to 162.210.90.20).
First line is the comment, line starts with the IP address:

```
# 2017-08-25 (username): /srv/raid5/test-volume accessible to 162.210.90.20
/srv/raid5/test-volume 162.210.90.20(rw,no_root_squash,no_subtree_check,sync)
```

### Reload All Exports:

```
exportfs -ra
```

This will do the job on Big-Disk Side.

## Mounting on Remote Machine

On the remote machine, you’ll need to mount the NFS share. Here’s how to do it:
To mount the NFS share on a folder, such as `/opt/test`, edit `/etc/fstab` and add a line that looks like this:

```
162.210.90.4:/srv/raid5/test-volume	/opt/test	nfs4	defaults,fg,retry=30
```

`retry=30` will keep on retrying mounting for 30 mins before giving up.
