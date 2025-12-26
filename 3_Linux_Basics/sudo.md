# The `sudo` command

- stands for `superuser do`
- gives temporarily privileges to another user (by default from the root user)
- it thus executes commands with elevated permissions, e.g. installing software
- requires user authentication (user's password - not root user's password!)
- the user must be allowed to use `sudo`
- `sudo` will sanitise (meaning: delete) most environment variables for the command we're calling. This is great, as it improves the security - but inheriting the environment variable won't work (unless preserved with `-E`)
- using `sudo` by prefixing any commands that need elevated permissions
- `sudo` will be stored in a session (by default 15 min) where no password is required to be entered again

**Options:**

- `-E` using the `-E` flag will preserve the current environment (and any env vars) of the active shell
- `-k` expires `sudo` session immediately after use (password will be required again)
- `-s` starts a shell with `sudo` privileges by default (security risk!) - This will start a shell as the root user
- `bash`/`zsh` same as above, starts a specific shell as the root user
- using `sudo` to start a program as a different user:

```bash
sudo -u [user] -g [group]

# e.g. will open bash as user 'Ben'
sudo -u ben

# e.g. will create a file for owner 'Ben'
sudo -u ben touch file.txt
```

## Configuring `sudo` access

- users that can use `sudo` are configured in `/etc/sudoers`
- we should use the `visudo` command to safely edit this file

```bash
sudo visudo
```

- we can allow access for specific users or groups

```bash
# user access to sudo
[username] ALL=(ALL:ALL) ALL

# group access to sudo
%[group] ALL=(ALL:ALL) ALL
```

**Parameters:**

- `username`: the rule should be applied to
- `ALL=`: hostname - if config file was for multiple machines
- `(ALL:ALL)`: the run configuration:
  - `ALL:` other user that the [username] can `sudo` into
  - `ALL` group that the [username] can `sudo` into
- `ALL`: command specification, e.g. `usr/bin/apt-get`
  - `NOPASSWD` to omit authorization for commands

```bash
# user access to sudo
[username] ALL=(ALL:ALL) NOPASSWD:ALL
```

- `@includedir /etc/sudoers.d` means we can specify other files to extend this config (best practice)
- create new file here

```bash
sudo visudo /etc/sudoers.d/[username]
```
