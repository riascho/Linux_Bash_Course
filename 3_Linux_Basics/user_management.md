# User Management

Linux has different users:

- **Root user**: highest privileges, user ID `0` (there can only be one root user)
- **Regular users**: limited privileges, but can be allowed temporarily root access through `sudo` command
- **Service users**: for specific tasks, e.g. safely run a webserver, database etc. (isolation)
- All users have a primary group, but can be assigned to unlimited additional groups

User information is stored in various files:

- `/etc/passwd`: contains basic user account information, readable by all users

  ```bash
  root:x:0:0:root:/root:/bin/bash
  # username : userID : groupID : user description : home directory : default shell
  ```

- `/etc/shadow`: stores encrypted user passwords and password aging information, only readable by the root user (or users with root privileges), `*` means no login/password enabled, `!` means password is locked

- `/etc/group`: contains information about the groups and their members, readable by all users

## Managing Users

### `useradd` - Create New Users

```bash
useradd [options] [username]
```

**Options:**

- `-m` create home directory (if we want regular user with login)
- `-d` set custom home directory, default path would be `/home/username`
- `-s` specify default shell
- `-g` specify primary group instead of default configuration (usually new group will be created with same name as username)
- `-G` add user to secondary groups

### `passwd` - Set User Passwords

```bash
passwd [options] [username]
```

**Options:**

- `-S` display password status, output:

  ```bash
  ria P 2025-12-22 0 99999 7 -1
  # username type-of-password last-changed minimum-age days-until-expiry warning-time enable-to-login-even-if-expired?
  ```

- `-d` delete password
- `-n` set minimum password age (days)
- `-x` set maximum password age (days) (to enforce password rotation for users)
- `-l` lock user account
- `-u` unlock user account

**Usage:**

```bash
passwd [username]
```

Sets password for username (if not specified it will use current logged-in user).

> Once a user has a password set, they will show up in the user account selection UI

### `usermod` - Modify User Accounts

```bash
usermod [options] [username]
```

**Options:**

- `-c` change user description (full name)
- `-s` change default shell
- `-d` change home directory (combine with `-m` to also move the existing home directory to the new location)
  > Might break relative paths that the user may have in use!
- `-l` change username
- `-g` change primary group
- `-G` change secondary group
- `-aG` add secondary group

### `userdel` - Delete User Accounts

```bash
userdel [options] [username]
```

**Options:**

- `-r` remove home directory and mail spool
- `-f` force removal even if user is still logged in (might also delete a group with the same name as this user - depending on system configuration)
