# File Permissions

file permissions allow to control access to files and directories and determine who can read, write and execute files.

There are 3 important levels of permissions:

- owner `u`
- group `g`
- others `o`

Types of permissions:

- Read (`r`/`4`): allows viewing file contents or listing directory contents
- Write (`w`/`2`): allows modifying the contents or creating / deleting files in a directory
- Execute (`x`/`1`): allows running a file as a program or changing into this directory

for example:

```bash
-rw-r--r-- 1 root root 0 Dec 26 16:45 test.txt
# permisions (read-write-execute):
# owner (root) rw-
# group (root) r--
# others r--
# file owner: root
# file group: root
```

## `chmod` command to assign permissions to a file

adding permissions with `+` or removing them with `-`

```bash
chmod u+x file.txt
# would give owner (u) executable rights

chmod g-w file.txt
# would remove the write permission for the group (g)

chmod o+r files
# would give other users (o) read access to this directory
```

## `chown` command to change owner / group of a file

```bash
chown [user]:[group] [filename]

#e.g.
sudo chown ria:FAMILY test.txt
# will change permissions to
-rw-r--r-- 1 ria FAMILY 7 Dec 29 17:46 test.txt
```

### using numeric values

by calculating the permissions (addition)

- first digit is for the owner
- 2nd digit is for the group
- 3rd digit is for all others

e.g.

```bash
chmod 754 file.txt
# 7 = 4+2+1 (rwx)
# 5 = 4+1 (r-x)
# 4 => (r--)

-rwxr-xr-- 1 ria FAMILY 7 Dec 29 17:46 file.txt
```

> `chmod 000` would remove all permissions

## File Permissions for directories

- Read (`r`): Access directory contents
- Write (`w`): Add or remove files (will also need execute `x` permissions for this though)
- Execute (`x`): Enter and traverse a directory

> using the `-R` flag will apply the permission changes to all sub-directories and files (recursively)

## `umask` command to set default permissions

The `umask` command allows us to specify who should be able to access new files, thus it's an important security feature. We start with some base permissions and then apply the `umask` command to either enhance or subtract those.

**Base Permissions:**

- `777` for directories (`rwx` for everybody)
- `666` for files (`rw-` for everybody)

For Example:
Setting a `umask` to `022` will change to:

- `755` for directories (`drwxr-xr-x`)
- `644` for files (`-rw-r--r--`)

> use `umask` command to show current umask value

```bash
umask [value]
# set umask value for current terminal session

UMASK=022
# set permanently in shell startup file (eg. ~/.bashrc)

# set umask permanently for all programs (incl UI!) in /etc/login.defs
# (change USERGROUPS_ENAB as well if user id = group id)
```

> setting a `umask` value of `7` will also result in `0` for files

## the sticky bit

The sticky bit is a special permission

- without the sticky bit (`0`) any user with `-wx` permissions for a directory can rename/delete files in it
- with the sticky bit set (`1`), only the owner (and root) can rename or delete a file

- This is commonly used on shared directories like `/tmp`.

```bash
chmod +t [directory]
# sets the sticky bit

chmod 1777 [directory]
# sets sticky bit (1) + rwx for all (777)

ls -ld /tmp
drwxrwxrwt 10 root root 4096 Dec 29 18:00 /tmp
# the 't' at the end indicates the sticky bit is set
```

## Setting SUID / SGID

Certain commands have additional privileges, e.g. `sudo`, `su`, `mount`. This is achieved with the so-called Set User ID (`SUID`) or Set Group ID (`SGID`), which is a special bit for executable files. The executable will gain the rights of the owner (instead of the user). This allows unprivileged users to access privileged resources.

> Could be a severe security issues though! `SUID`/`SGID` should be set for executable **binary** files only (not executable **scripts** like `.py` or `.sh`).

```bash
ls -l /usr/bin/sudo
-rwsr-xr-x 1 root root 335120 Jun 25  2025 /usr/bin/sudo

chmod u+s [filename]
# sets SUID bit for user

chmod g+s [filename]
# sets SGID bit for group

# e.g. Python
-rw-r-sr-- 1 root root 7845032 Jan  1 14:30 python3
```

- lowercase `s`: SUID bit + execute bit
- uppercase `S`: SUID bit without execute bit

## Best Practices

- prefer groups to manage privileges
- use meaningful group names
- follow the principle of least privilege (assigning users only the necessary permissions and group memberships required -> don't give write access to everybody)
- minimize the number of users with elevated privileges
- keep group memberships up-to-date
-
