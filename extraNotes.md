- to allow a file to be executable: `chmod +x [file name]`

- using `ping` to communicate with a server (handy to check internet connection etc.)

- `ip addr show` - shows IP address info (only linux)
  `ifconfig -a` - for Mac

- `uptime` is a command that shows how long a system has been running

- use the `unzip` command to unpack zip files:
  `unzip archive.zip -d [destination for extraction]`
  `-d` sets the destination directory, otherwise the zip file would be extracted in current working directory

- the `watch` command is used to automatically re-execute a command.

```bash
watch -n 0.5 grep ctxt /proc/12345/status
# refresh every 0.5 seconds to get the context switches of process 12345
```

- `cmatrix` fun little programm that turns the terminal into matrix

## programs

There is 2 types of programs:

- built-in shell programs
- executable file

- `type [program name]` tells which type it is (and path)

## `stty`

The `stty` command is a tool to change the terminal line settings

```bash
stty tostop
# turns on that any program is run until it produces output. It is then being stopped (paused)

stty -tostop
# turns this feature back off
```

## `lsb`

Find out which linux distribution you have:

```bash
lsb_release -a
```

## `date` command

The `date` command prints (or sets) the system date and time. On its own it shows the current date/time in the default format:

```bash
date
# Mon 21 Jul 2026 14:32:07 CEST
```

Add a `+FORMAT` argument to control the output. Everything after `+` is a template where `%`-sequences are replaced with date/time parts, and any other text is printed literally.

```bash
date +%T          # 14:32:07        — time (HH:MM:SS)
date +%H:%M       # 14:32           — hours and minutes only
date +%F          # 2026-07-21      — full date (YYYY-MM-DD), ISO 8601
date +%A          # Tuesday         — weekday name
date +%s          # 1784816927      — Unix epoch (seconds since 1970-01-01)

date "+Today is %A, %d %B %Y"   # Today is Tuesday, 21 July 2026
date +%Y-%m-%d_%H-%M-%S         # 2026-07-21_14-32-07  (handy for filenames)
```

Common format specifiers:

| Spec        | Meaning                | Example      |
| ----------- | ---------------------- | ------------ |
| `%Y`        | Year (4 digits)        | `2026`       |
| `%m`        | Month (01–12)          | `07`         |
| `%d`        | Day of month (01–31)   | `21`         |
| `%H %M %S`  | Hour / minute / second | `14 32 07`   |
| `%T`        | Time = `%H:%M:%S`      | `14:32:07`   |
| `%F`        | Date = `%Y-%m-%d`      | `2026-07-21` |
| `%A` / `%a` | Weekday full / short   | `Tuesday`    |
| `%B` / `%b` | Month full / short     | `July`       |
| `%s`        | Unix timestamp         | `1784816927` |

Useful extras:

```bash
date -u                    # show time in UTC instead of local timezone
date -d "next friday"      # GNU date: a date relative to now
date -d "2026-12-25" +%A   # what weekday is a given date? → Friday
```

> `-d` (arbitrary date strings) is a GNU coreutils feature, so it works on Linux but not on macOS/BSD `date`.

## `id`

```bash
# shows current user uid and gid (groups)
id
```

## FTP & etc.

**FTP (File Transfer Protocol):** A standard network protocol used for transferring files between a client and a server. However, FTP itself is not secure.

**FTPS (FTP Secure):** This is an extension to FTP that adds support for the Transport Layer Security (TLS) and the Secure Sockets Layer (SSL) cryptographic protocols. FTPS encrypts the data being transferred to enhance security.

**SFTP (SSH File Transfer Protocol):** Despite its name, SFTP is not related to FTP. SFTP is an extension of SSH (Secure Shell) and provides a secure method to transfer files over a network. It uses the SSH protocol to encrypt and transfer data, making it inherently more secure compared to FTP.

**SSL (Secure Sockets Layer):** A standard security technology for establishing an encrypted link between a server and a client. SSL is used in various protocols, including FTPS, to secure the transmission of data.

**SSH (Secure Shell):** A cryptographic network protocol for operating network services securely over an unsecured network. SSH is the basis for SFTP, providing an encrypted connection for file transfers and secure command execution.

**In summary:**
`FTPS` enhances standard `FTP` by adding `SSL`/`TLS` for encryption.
`SFTP` uses the `SSH` protocol to securely transfer files, and it is entirely different from `FTP`.
`SSL` is the protocol that provides security for communication, while `SSH` is the protocol used for secure remote login and file transfer.

## File Locking `flock`

We can use the `flock` command to lock a file. A file can only be locked by one program at the time. This is handy for cronjobs and running scripts to they don't affect each other (e.g. database writings)

```bash
# lock file during ping program
flock file.txt ping google.com

flock -n -E 0 file.txt ping google.com #if file.txt is locked already the next flock command will just exit (-n) with error code 0 (-e 0 = everything okay)
```
