# Shell Configuration

## Shell Types

There are different types of shells with different startup modes for bash:

- **Interactive** - Can execute commands
- **Login** - Asks for user and password

### Shell Type Categories

| Type                             | Description              | Examples                                                                 |
| -------------------------------- | ------------------------ | ------------------------------------------------------------------------ |
| Interactive login shells         | Full startup required    | `tty`, `ssh`                                                             |
| Interactive non-login shells     | UI already started       | `bash`, `zsh`                                                            |
| Non-interactive non-login shells | Script execution         | bash scripts                                                             |
| Non-interactive login shells     | Remote command execution | Sending a command with `ssh` to a remote server without starting a shell |

## The SHELL Variable

The `SHELL` variable stores the path to the user's **default shell** (not the currently active shell).

### Changing the Default Shell

To modify it:

```bash
chsh -s "/bin/bash"
```

> **Note:** The shell must be listed in `/etc/shells` (can be found in the root directory `~`).

⚠️ However, it might be overwritten by the terminal app.

### Default Configuration Loading

By default, the shell loads its configurations from:

- `.bashrc` (for Bash)
- `.zshrc` (for Zsh)

## Configuration Files

### File Types

| File            | Purpose                                      | Used By                                                                            |
| --------------- | -------------------------------------------- | ---------------------------------------------------------------------------------- |
| `.profile`      | POSIX-generic login shell config             | `sh`, `dash`, `ksh`, and by Bash only if no `.bash_profile` or `.bash_login` exist |
| `.bash_profile` | Bash-specific login shell config             | Bash (if this exists, Bash won't read `.profile`)                                  |
| `.bashrc`       | Bash config for interactive non-login shells | Bash (e.g., subshells, many terminal tabs)                                         |

### Why Both `.bashrc` and `.bash_profile`?

**Separation of Concerns:**

1. **Login-only setup (per session)** → Profile files

   - Environment exports
   - `PATH` bootstrapping
   - Agents

2. **Interactive-only setup (per shell)** → `.bashrc`
   - Prompt customization
   - Aliases
   - Completions
   - Shell options

**Compatibility:**

- `.profile` works for non-Bash shells
- `.bash_profile` is Bash-only

## Bash Startup Files

Bash loads different files on startup depending on the shell type.

### Interactive Login Shell

No UI has been started, full startup needed.

**Load order:**

1. `/etc/profile`
2. The **first found** of:
   - `~/.bash_profile`
   - `~/.bash_login`
   - `~/.profile`

### Interactive Non-Login Shell

UI already started, environment variables configured, system already setup.

**Loads:**

- `~/.bashrc` - Contains bash code and env vars

### Non-Interactive Shell (Scripts)

Bash looks for an environment variable named `$BASH_ENV` and, if set, will try to execute this file (and ignore `PATH`).

### macOS Exception

⚠️ **On Mac:** All interactive shells load files for login-shells!

## Applying Configuration Changes

### Reload Configuration

When changing a config file, remember to:

- **Launch a new shell** to load the config, OR
- Use `source` to reload in the current shell

### The `source` Command

The command `source` imports and executes a file within the same shell.

```bash
source ~/.bashrc
```

## Common Configuration Additions

### Adding to PATH

You can add binary directories to your `PATH` variable in the config file, so that the shell loads the custom path addition.

**Example:**

```bash
PATH="${PATH}:/usr/maria/bin"
```

### Creating Aliases

You can also add **aliases** to a startup file.

**Example:**

```bash
alias gohome='cd ~'
```

**Important:** Remember to use single quotes `'` so as not to make shell expansions (otherwise `~` would be expanded to the root directory immediately!).

### Managing Aliases

**List all existing aliases:**

```bash
alias
```

**Remove an alias:**

```bash
unalias gohome
```

## Configuring the Shell: `set`

Allows us to configure options that are inherited from the `sh` shell.

### Syntax

```bash
set -[feature]  # Enable a feature
set +[feature]  # Disable a feature
```

### Example Features

**Enable xtrace (debugging):**

```bash
set -x
```

Enables xtrace - each command that the shell executes will be printed (useful for debugging).

**More options:** https://www.gnu.org/software/bash/manual/html_node/The-Set-Builtin.html

## Configuring the Shell: `shopt`

We can configure options specific to Bash, and not part of the `sh` shell.

### Syntax

```bash
shopt -s [optname]  # Enable configuration option
shopt -u [optname]  # Disable configuration option
```

### Example Options

**Auto CD:**

```bash
shopt -s autocd
```

No need to use `cd` anymore for navigating (just type the directory name).

**CD Spell Correction:**

```bash
shopt -s cdspell
```

Helps to navigate even with typos in directory names.

**More options:** https://www.gnu.org/software/bash/manual/html_node/The-Shopt-Builtin.html
