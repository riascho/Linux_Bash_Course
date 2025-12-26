# Groups on Linux Systems

Groups help to organize users with similar access rights, e.g. printer access group. They help simplifying permission management, enhance collaboration and resource sharing as well as controlling access to files and directories. Overall, they help to strengthen system security.

- each user has a primary groupd and zero to many secondary groups

## Primary Group

- stored in `/etc/passwd`
- default ownership for new files

```bash
-rw-rw-r--  1 ria ria    0 Dec 24 18:11 test.txt
# owner group
```

## Secondary Group

- allow multiple memberships
- stored in `/etc/group`
- allows for fine grained access rights to system

```bash
ria:x:1000:bemshima
# owner :x:group ID : members of group
```

- to list a user's groups:

```bash
groups [username] [username]
#
```

## Existing Groups in Ubuntu

- `root`: the superuser group with administrative privileges, allowing complete control over the system, there can only be one root user but other users can be associated with the root group
- `sudo`/`wheel`: members can use `sudo` to give admin rights, may also be called `wheel`
- `adm`: allows members to read log files, e.g. in `/var/log/syslog`
- `lpadmin`/`lp`: members may manage printers and print queues (CUPS)
- `www-data`: a group for web server processes (such as Apache or Nginx), gives access to web content
- `plugdev`: allows members to manage pluggable devices (USB sticks, external harddisks, etc.)

## `usermod` command to manage Group members

To add a user to a group:

```bash
usermod [options] [username]
```

**Options:**

- `-g` changes primary group
- `-G` changes primary group (use comma for several groups, overwrites all group assignments -> this how you would remove groups)
- `-aG` changes primary group

## `adduser` command to manage group members

- `adduser [user] [group]` to add a user to a group
- `deluser [user] [group]` to delete a user from a group

## `groupadd` command to create a group

```bash
groupadd [options] [groupname]
```

- group info is then stored in the `/etc/group` file

**Options:**

- `-g`: option to set custom group ID (GID)

## `groupmod` command to change a group

```bash
groupmod [options] [groupname]
```

- this will update `/etc/group` and `/etc/passwd`

**Options:**

- `-n`: change name of the group
- `-g`: change GID

## `groupdel` command to delete a group

```bash
groupdel [groupname]
```

- this will update `/etc/group` and `/etc/passwd`
- this command will fail if we try to delete a primary group of a user

  > will not delete group-owned files!
