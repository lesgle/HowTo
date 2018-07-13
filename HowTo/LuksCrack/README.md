# Brute forcing LUKS protected device

Encrypt test device. In my case it is 8GB flash drive.:

```txt
root@hostname:~# fdisk -l
Disk /dev/sda: 7.5 GiB, 8015314944 bytes, 15654912 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

root@hostname:~# cryptsetup luksFormat /dev/sda

WARNING!
========
This will overwrite data on /dev/sda irrevocably.

Are you sure? (Type uppercase yes): YES
Enter passphrase:
Verify passphrase:
root@hostname:~# cryptsetup luksOpen /dev/sda test
Enter passphrase for /dev/sda:
```

Create file system on mapped device.

```txt
root@hostname:~# mkfs.ext4 /dev/mapper/test
mke2fs 1.43.4 (31-Jan-2017)
Creating filesystem with 1956352 4k blocks and 489600 inodes
Filesystem UUID: 6a969f1b-862b-4dfa-9904-912305da4098
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done
Writing inode tables: done
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

Mount encrypted volume.

```txt
root@hostname:~# mount /dev/mapper/test /test/
root@hostname:~# df -h
Filesystem        Size  Used Avail Use% Mounted on
/dev/mapper/test  7.3G   34M  6.9G   1% /test
```

Umount and close luks device.

```txt
root@hostname:~# umount /test
root@hostname:~# cryptsetup luksClose test
```

Snap luks header.

```txt
root@hostname:~ # dd if=/dev/sda of=test.header bs=512 count=4097
4097+0 records in
4097+0 records out
2097664 bytes (2.1 MB, 2.0 MiB) copied, 0.139339 s, 15.1 MB/s
```

Copy the header to crack station and start brute forcing. In my case it is VM with Xeon CPU.

```txt
F:\hashcat-4.1.0>hashcat64.exe -m 14600 F:\test.header -a 3 ?d?d?d?d?d?d
hashcat (v4.1.0) starting...

OpenCL Platform #1: Intel(R) Corporation
========================================
* Device #1: Intel(R) Xeon(R) CPU D-1521 @ 2.40GHz, 1021/4087 MB allocatable, 8MCU

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates

Applicable optimizers:
* Zero-Byte
* Single-Hash
* Single-Salt
* Brute-Force
* Slow-Hash-SIMD-LOOP

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

F:\test.header:123456

Session..........: hashcat
Status...........: Cracked
Hash.Type........: LUKS
Hash.Target......: F:\test.header
Time.Started.....: Tue May 22 12:33:39 2018 (23 secs)
Time.Estimated...: Tue May 22 12:34:02 2018 (0 secs)
Guess.Mask.......: ?d?d?d?d?d?d [6]
Guess.Queue......: 1/1 (100.00%)
Speed.Dev.#1.....:       89 H/s (8.62ms) @ Accel:256 Loops:128 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 2048/1000000 (0.20%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 0/100000 (0.00%)
Candidates.#1....: 123456 -> 135500
HWMon.Dev.#1.....: N/A
```

Another example with custom charset.

```txt
F:\hashcat-4.1.0>hashcat64.exe -m 14600 F:\test.header -a 3 -1 123456 ?1?1?1?1?1?1
hashcat (v4.1.0) starting...

F:\test.header:123456

Session..........: hashcat
Status...........: Cracked
Hash.Type........: LUKS
Hash.Target......: F:\test.header
Time.Started.....: Tue May 22 12:44:57 2018 (22 secs)
Time.Estimated...: Tue May 22 12:45:19 2018 (0 secs)
Guess.Mask.......: ?1?1?1?1?1?1 [6]
Guess.Charset....: -1 123456, -2 Undefined, -3 Undefined, -4 Undefined
Guess.Queue......: 1/1 (100.00%)
Speed.Dev.#1.....:       91 H/s (8.36ms) @ Accel:256 Loops:128 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 2048/46656 (4.39%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 0/7776 (0.00%)
Candidates.#1....: 123456 -> 115144
HWMon.Dev.#1.....: N/A
```