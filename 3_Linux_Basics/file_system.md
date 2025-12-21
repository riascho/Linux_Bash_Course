# Linux File System

## The Unix Philosophy: Everything is a File

One of the core principles of Unix/Linux is that **everything is treated as a file**. This unified approach simplifies system operations and provides a consistent interface for interacting with different system resources.

## File Types

Linux recognizes several different types of files, each identified by a specific character in the file listing:

| Type             | Symbol | Description                                                                |
| ---------------- | ------ | -------------------------------------------------------------------------- |
| Ordinary file    | `-`    | Regular files (text, binary, executables)                                  |
| Directory        | `d`    | Container for files and other directories                                  |
| Symbolic link    | `l`    | Reference/shortcut to another file or directory                            |
| Character device | `c`    | Device that handles data character by character (e.g., keyboard, terminal) |
| Block device     | `b`    | Device that handles data in blocks (e.g., hard drives)                     |
| Named pipe       | `p`    | Special file for inter-process communication                               |
| Socket           | `s`    | Special file for network communication                                     |

### Viewing File Types

To see the type of a file, use the `ls -l` command:

```bash
ls -l [folder/file]
# The first character indicates the file type
# Example output:
# drwxr-xr-x   7 ria  staff   224 Dec  3 17:03 a_folder
# ^
# └─ 'd' indicates this is a directory
```

To include hidden files (those starting with `.`):

```bash
ls -la [folder/file]
```

---

## Symbolic Links (Symlinks)

### What are Symlinks?

Symbolic links, also called **softlinks**, are special files that serve as references or "shortcuts" to another file or directory. When you access a symlink, the system automatically redirects you to the target file.

**Key characteristics:**

- Acts as a pointer to another file or directory's path
- Can cross filesystem boundaries
- Can link to directories
- If the target is deleted, the symlink becomes "broken"
- Access rights are determined by the target file, not the symlink itself

### Common Use Cases

- Creating convenient access points to frequently used directories
- Maintaining multiple references to the same file without duplication
- Linking configuration files across different locations
- Creating version-agnostic paths (e.g., `/usr/bin/python` → `/usr/bin/python3.11`)

### Creating a Symlink

Use the `ln` command with the `-s` flag:

```bash
ln -s [target] [link_name]

# Example: Create a symlink to Desktop
ln -s ~/Desktop/ linkToDesktop

# Now you can navigate using the symlink
cd linkToDesktop
```

**Note:** Changes made through either the symlink or the original path affect the same underlying file—both paths remain synchronized.

---

## Hard Links

### What are Hard Links?

A hard link is a directory entry that points directly to an existing inode (the actual data on disk). Unlike symlinks, hard links are **not separate files**—they are simply additional names for the same file.

**Key characteristics:**

- Multiple filenames pointing to the same inode
- All hard links are equal—there's no "original" file
- Must be on the same filesystem (cannot cross filesystem boundaries)
- Cannot be created for directories (with rare exceptions)
- The file data is only deleted when all hard links are removed

### Creating a Hard Link

Use the `ln` command without the `-s` flag:

```bash
ln [target] [hardlink]

# Example:
ln original.txt copy.txt
# Both files now point to the same data
```

### Copying Files with Hard Links

You can efficiently copy an entire directory structure using hard links, which saves disk space:

```bash
cp -al [source] [destination]
```

This creates a copy of the source folder structure where all files are hard-linked rather than duplicated. **No additional storage is needed** for the file content—only the directory structure uses new space.

## Inode

An inode is a filesystem data structure that stores a file's metadata and pointers to its data, but not the filename. It includes:

- File size
- Permissions
- Owner
- Timestamps
- Location of the file's data blocks

Filenames are stored in directories and mapped to inodes.

### Inode Limits

When a file is created, the filesystem allocates an inode to store its metadata. This inode occupies disk space that is permanently reserved for metadata and cannot be reused for file data. Most filesystems create a fixed number of inodes when the filesystem is formatted. If a system creates many small files, it can run out of available inodes even if there is still free disk space. When this happens, no new files can be created because there are no remaining inodes to store their metadata.

Check inode usage:

```bash
df -if
# disk free -inodes human-readable
```

---

## Symlink vs. Hardlink

### Hard Link

- Another name for the same file
- Both filenames point directly to the same inode (the file's data on disk)
- No "original" file—all hard links are equal

### Symbolic Link (Symlink / Soft Link)

- A special file that contains a path to another file
- Points to the name/location, not directly to the data
- Like a shortcut

### Visual Analogy

- **Hard link:** Two labels on the same physical box
- **Symlink:** A note that says "the box is in room B"

### When to Use Which

**Use hard links when:**

- You want guaranteed access to the data
- You don't want links to break if files are moved
- Everything is on the same filesystem

**Use symlinks when:**

- You need cross-filesystem links
- You want to link directories
- You want shortcut-like behavior

## Buffered vs. Unbuffered I/O

### Unbuffered I/O

- Transfers data **directly** between the program and the I/O device
- Provides **immediate, real-time access** to data
- Allows **precise control** over data flow and timing
- Typically used when low latency is critical

**Example:** Reading input directly from a keyboard

### Buffered I/O

- Uses a **temporary buffer** to store data before sending or receiving
- **Reduces I/O operations** by processing data in chunks
- **Improves performance**, especially for disk and network access
- Helps with **data integrity and error checking**

**Examples:**

- Reading files from disk
- Writing data to disk in blocks

### Key Difference

- **Unbuffered I/O** → faster access, more control, higher overhead
- **Buffered I/O** → better performance overall, less frequent device access

## Devices

A device refers to a physical or virtual entity that can be accessesd through a file-like interface between the operating system and various hardware or virtual components. They allow applications and users to interact with these components by reading from and writing to their corresponding device files. Devices are found in the `~/dev/` folder

#### Character Devices (`c`)

Character devices handle data one character (byte) at a time. They process input and output sequentially without buffering. Common examples include keyboards, mice, terminals, and serial ports. Data flows in a continuous stream rather than in fixed-size blocks.

#### Block Devices (`b`)

Block devices handle data in fixed-size blocks (typically 512 bytes to 4 KB). They allow random access to data and are commonly used for storage devices like hard drives, SSDs, and USB drives. Block devices are more efficient for large data transfers.

#### Pseudo Devices

Pseudo devices (or virtual devices) are special files that don't correspond to physical hardware. They provide software interfaces for system operations:

- `/dev/null` - discards all data written to it
- `/dev/zero` - provides unlimited null bytes
- `/dev/random` - generates random data
- `/dev/urandom` - generates pseudorandom data
- `/dev/stdin`, `/dev/stdout`, `/dev/stderr` - standard I/O streams

## File System Hierarchy Standard

| Directory     | Purpose                            | Details                                                                                                      |
| ------------- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| `/`           | Root directory                     | Top-level directory of the entire filesystem; everything on the system resides under this directory          |
| `/bin`        | Essential user binaries            | Command binaries required for all users; includes critical utilities like `ls`, `cat`, `cp`                  |
| `/boot`       | Boot files                         | Contains bootloader files, kernel images, and RAM disk files required to start the Linux system              |
| `/dev`        | Device files                       | Special files representing hardware and software devices (e.g., `tty`, disk drives, USB devices)             |
| `/etc`        | System configuration               | System-wide configuration files (e.g., `systemd`, network settings, user authentication)                     |
| `/home` (`~`) | User home directories              | Personal directories for users containing user-specific settings, documents, and files                       |
| `/lib`        | System libraries                   | Library files supporting binaries in `/bin` and `/sbin` (e.g., shared libraries, Python versions)            |
| `/media`      | Removable media mount points       | Mount points for removable storage (e.g., USB drives, SD cards, external hard drives)                        |
| `/mnt`        | Additional filesystem mount points | Mount points for additional filesystems that are usually non-removable and not shown in the UI               |
| `/opt`        | Optional software packages         | Third-party or optional application software packages (e.g., Homebrew, custom applications)                  |
| `/proc`       | Process information                | Virtual filesystem providing runtime information about processes and kernel (see [proc](#proc-files-procfs)) |
| `/root`       | Root user home                     | Personal data directory for the root user; equivalent to `/home` for the root user                           |
| `/run`        | Runtime data                       | Temporary runtime data; files are removed/emptied during boot or discarded on shutdown                       |
| `/sbin`       | System binaries                    | Essential system binaries typically used by the root user for system administration and maintenance          |
| `/srv`        | Service data                       | Files served by services (FTP, web servers, etc.); alternative to `/var` for service data                    |
| `/sys`        | Kernel/device information          | Virtual filesystem providing information about devices, drivers, and kernel features; useful for monitoring  |
| `/tmp`        | Temporary files                    | Temporary files created by system and users; typically cleared on reboot to save disk space                  |
| `/usr`        | Shareable user data                | Read-only, shareable user programs and data not unique to the system; contains many standard programs        |
| `/usr/local`  | Local user software                | User-installed programs and data not shared across multiple computers (e.g., proprietary software)           |
| `/usr/sbin`   | User system binaries               | System binaries for administrative tasks; less critical than those in `/sbin`                                |
| `/var`        | Variable data                      | Files with changing content such as logs, databases, websites, and email; critical for system backups        |
| `/snap`       | Snap packages                      | Snap package manager directory; contains sandboxed application packages                                      |
| `/lost+found` | Filesystem recovery                | Directory used by filesystem repair tools to store recovered files after disk errors or unexpected shutdowns |

### `/proc` files (procfs)

If we want to inspect our system, we can look into files in the `/proc` folder:

- `/proc/cpuinfo`: information about the system's CPUs, like vendor, model and speed
- `/proc/meminfo`: information about the memory usage of the system, including physical and swap memory
- `/proc/version`: information about the kernel, gcc and the operating system
- `/proc/uptime`: displays time the system has been up (in seconds) and the idle time
- `/proc/loadavg`: shows system load averages for the past 1, 5 and 15 minutes, number of currently running processes/threads, and the last PID
