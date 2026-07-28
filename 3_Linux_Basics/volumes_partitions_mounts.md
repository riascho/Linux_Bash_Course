# Volumes & Partitions & Mounts

## Partition

A partition is a part of a physical drive. It is separated from other parts of the drive. Each partition will need a filesystem.

| Filesystem | Main OS  | Journaling | Max File Size | Max Volume Size | Snapshots | Notes                                            |
| ---------- | -------- | ---------- | ------------- | --------------- | --------- | ------------------------------------------------ |
| ext4       | Linux    | Yes        | 16 TB         | 1 EB            | No        | Default on most distros; stable and reliable     |
| xfs        | Linux    | Yes        | 8 EB          | 8 EB            | No        | High performance with large files & parallel I/O |
| btrfs      | Linux    | Yes (CoW)  | 16 EB         | 16 EB           | Yes       | Copy-on-write, snapshots, built-in RAID          |
| NTFS       | Windows  | Yes        | 16 TB         | 256 TB          | Yes (VSS) | Default modern Windows; permissions & encryption |
| ReFS       | Windows  | Yes        | 35 PB         | 35 PB           | Yes       | Resilient; auto-heals data; Windows Server       |
| FAT32      | Cross-OS | No         | 4 GB          | 2 TB            | No        | Very old; universal but limited                  |
| exFAT      | Cross-OS | No         | 16 EB         | 128 PB          | No        | USB & SD cards; no 4 GB limit; cross-platform    |
| APFS       | Mac      | Yes (CoW)  | 8 EB          | 8 EB            | Yes       | Default modern macOS; optimized for SSDs         |

**Key terms:**

- **Journaling** — the filesystem keeps a log of changes before writing them, so it can recover quickly after a crash or power loss.
- **CoW (Copy-on-Write)** — instead of overwriting data in place, new data is written to a new location. This makes snapshots cheap and protects against corruption.
- **Snapshots** — a point-in-time, read-only copy of the filesystem, useful for backups and rollbacks.
- **RAID** — combining multiple physical disks into one logical unit for better performance and/or redundancy (e.g. mirroring data across disks so one can fail without data loss). Some filesystems like btrfs can do this themselves.
- **EB / PB / TB / GB** — Exabyte > Petabyte > Terabyte > Gigabyte (each step is ~1000× larger).

```bash
# using the parted program we can create a new parition
parted
mkpart /dev/sdb1

# make filesystem for that partition
mkfs.ext4 /dev/sdb1

print # shows all partitions in parted
```

## Volume

A volume is a logical storage unit (usable space) on the computer. It usually appears as an accessible drive or partition. It can be more than just a partition - multiple partitions can be combined into one logical volume (LVM). Or a volume can be stored on another computer and accessed through the network.

## Mount

The idea is to connect the filesystem of the volume to our directory tree. This makes it accessible to our programs.

**Mountable drives:**

- external removable media (usually mounted into subfolder of `/media` - will also show up in the user interface)
- external storage servers (e.g. FTP)
- internal permanent volumes (usually mounted into subfolder of `/mnt`)
- folders into other folders (bind mount)

### `/dev`

The `/dev` directory in a Linux system is special as it contains device files that represent hardware devices, allowing the operating system and applications to interact with the hardware through files. Here's a bit more about what you can typically find under `/dev`:

**Device Files:** These files represent physical devices like hard drives, USB devices, and peripherals. For example, you might see entries like `/dev/sda` for a hard drive or `/dev/tty` for terminal devices.

**Character Devices:** These allow unbuffered access to the hardware, meaning you can read and write data one byte (or character) at a time. An example would be a keyboard or serial port.

**Block Devices:** These work with blocks of data and are typically used for storage devices like hard drives and USB drives, where data can be read or written in blocks.

**Virtual Devices:** In addition to physical devices, `/dev` can hold entries for virtual devices provided by the kernel, which may not correspond directly to physical hardware but are necessary for system operations.

Overall, the `/dev` directory provides a critical interface between the operating system and physical or virtual devices, enabling processes and users to read from and write to these devices effectively.

### How to manually mount a drive

1. get name of drive to mount, e.g. `/dev/sdb1` (with the `/dev` path)

```bash
# list block devices on system (also show information about filesystem)
lsblk -f
```

2. create folder to mount drive into `/mnt`, e.g. backups

```bash
mkdir /mnt/backups
```

3. mount the drive

```bash
mount [device] [mount_point]
# example
mount /dev/sdb1 /mnt/backups
```

4. options

```bash
# specify filesystem manually
mount -t ext4 /dev/sdb1 /mnt/backups

# mount with options (ext4)
mount -o ro ext4 /dev/sdb1 /mnt/backups # read-only
-o rw # read-write (defaults)
-o noexec # disables execution of executable files
-o nosuid # disables set-user-identifier and set-group-identifier - prevents potential security risks
-o noatime # no update of accesstime when file is being read

# mount with options (exFAT)
-o gid=1001 # all files should be owned by group id 1001
-o uid=1001 # all files should be owned by user id 1001
-o umask=0027 # sets permissions for filesystem

# show more options
man mount

# verify the mount
mount # shows all mounts
df -h # disk free with usage stats
```

5. unmount the drive

```bash
umount /dev/sdb1 #or
umount /mnt/backups
```

### mounting on boot

We can use the static file system information (`/etc/fstab` configuration file) to define how storage devices and partitions should be mounted. It is being read during boot and allows us to automatically mount volumes.

```bash
# each line represents a filesystem to be mounted
# fields (columns) are separated by spaces or tabs

1. device identifier # (UUID or device path)
2. mount point
3. filesystem type
4. mount options # defaults (rw,suid,dev,exec,auto,nouser,async)
5. dump option # (backup utility, 0 = no backup)
6. filesystem check order # (fsck priority, 1=root, 2=non-root)

# e.g.
UUID=аec067b7-c3cc-4b0d-97da-c4be187204f9 /mnt/backups ext4 defaults,nosuid,noexec 0 2
```

- use `blkid` to print the universally unique identifier for a device
- use `mount -a` to refresh mounts after editing `/etc/fstab`

## Device Monitoring

### SMART

Most physical devices can proactively report their own health using the `SMART` (Self-Monitoring, Analysis, and Reporting Technology) protocol. SMART primarily evaluates hardware-level issues of the drives and does not address problems related to partition tables or file systems, which require different diagnostic methods.

```bash
# need to have smartmontools installed
smartctl --all /dev/[device_name]

# on mac
smartctl --all /dev/disk1
```

### File system checks

- can be used to fix a broken filesystem
- drive needs to be unmounted for this
- drive should not be encrypted

```bash
# trigger check on partition
fsck /dev/sdb2

# some systems might need the file system type
fsck.ext4 /dev/sdb2

# file system check on the kernel (add boot option in GRUB menu)
fsck.mode=force
```

- linux only does automatic file system checks after a drive was unmounted unproperly
- this is configured in the `/etc/fstab` config -> last column (0=no auto fs check, 1=auto fs check)
- we can still set auto checks (regardless of unproper mount) in two ways

1. time-based check interval (e.g. every 30 days)
2. mount count based check interval (e.g. every 5 mounts)

```bash
# check current settings for automatic filesystem checks
tune2fs -l /dev/sdb2 | grep -i -F 'check' # time-based
tune2fs -l /dev/sdb2 | grep -i -F 'mount' # mount-based

# set automatic checking
tune2fs -i 6m /dev/sdb2 # every 6 months
tune2fs -c 30 /dev/sdb2 # every 30 mounts
```

## Resizing File System

- not all file systems can be resized (e.g. `exFAT` can't but `ext4` can)

1. file system should be unmounted
2. run file stystem check before resizing
3. resize filesystem with new size `resize2fs /dev/sdb2 10G`
4. resize partition with `resizepart` in `parted` programm
   4.a. make sure to check the correct size unit (MiB vs. MB)
   4.b. change unit in `parted` with `unit KiB`
5. for increasing a fs we need to resize partition first and then the filesystem!
