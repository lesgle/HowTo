# ZFS - Export snapshot to encrypted file

This post shows some basic steps how to install ZFS on Ubuntu 16.04, create ZFS pool and file system. It will cover snapshot creation and export to encrypted file.

Install ZFS tools

```text
apt install zfsutils-linux
```

Create ZFS pool called Pool1 from single disk (/dev/xvdb)

```text
zpool create Pool1 /dev/xvdb -f
```

Check newly created pool.

```text
zpool list
NAME   SIZE ALLOC  FREE EXPANDSZ  FRAG  CAP  DEDUP  HEALTH  ALTROOT
Pool1 49.8G   50K 49.7G        -    0%   0%  1.00x  ONLINE  -
```

Create ZFS file system.

```text
zfs create Pool1/Volume1
```

Check ZFS file systems.

```text
zfs list
NAME            USED  AVAIL  REFER  MOUNTPOINT
Pool1          81.5K  48.2G    19K  /Pool1
Pool1/Volume1    19K  48.2G    19K  /Pool1/Volume1
```

Create ZFS snapshot.

```text
zfs snapshot Pool1/Volume1@snapshot1
```

List snapshots.

```text
zfs list -t snapshot
NAME                      USED  AVAIL  REFER  MOUNTPOINT
Pool1/Volume1@snapshot1      0      -    19K  -
```

Export ZFS snapshot, archive it using gzip and encrypt it by password. (Note: `-pass file:/path/to/file` can be used, otherwise password will be shown in `top` output).

```text
zfs send Pool1/Volume1@snapshot1 | gzip | openssl enc -aes-256-cbc -pass pass:Your_Password > snapshot1.gz.enc
```

Decrypt the snapshot file.

```text
openssl enc -d -aes-256-cbc -pass pass:Your_Password -in snapshot1.gz.enc -out snapshot1.gz
```

Mount snapshot as dedicated ZFS volume.

```text
gzip -c -d snapshot1.gz | zfs receive Pool1/Volume1_Snapshot
```

Check ZFS volumes.

```text
zfs list
NAME                     USED  AVAIL  REFER  MOUNTPOINT
Pool1                    273K  48.2G    19K  /Pool1
Pool1/Volume1             19K  48.2G    19K  /Pool1/Volume1
Pool1/Volume1_Snapshot    19K  48.2G    19K  /Pool1/Volume1_Snapshot
```