# Terminal Basics

## Bash Version

Check the current Bash version (only works when running in Bash):

```bash
echo "${BASH_VERSION}"
```

**Note:** Launch Bash by typing `bash` in the terminal.

## Echo Command

The `echo` command prints text to the terminal.

### Basic Usage

```bash
echo 'Hello World'  # Use single quotes for strings
```

### Options

- **`-n`**: Omit the newline at the end

  ```bash
  echo -n 'Hello World'
  ```

- **`-e`**: Evaluate escape characters in the string
  ```bash
  echo -e 'Hello \n World!'
  ```

## Navigation

### Directory Shortcuts

- **Root folder:** `cd /`
- **Home folder:** `cd ~`

## ls Command

List directory contents with various options:

| Option     | Description                                    |
| ---------- | ---------------------------------------------- |
| `-a`       | Show hidden files                              |
| `-t`       | Sort by modification time                      |
| `-r`       | Reverse order                                  |
| `--color=` | Set color output: `never`, `always`, or `auto` |

## Running Multiple Commands

### Sequential Execution (`;`)

Run commands one after another, regardless of success:

```bash
cmd1; cmd2
```

### Conditional Execution (`&&`)

Run the second command only if the first one succeeds:

```bash
cmd1 && cmd2
```

## Getting Help

### Using `--help` or `-h`

Most commands provide help information:

```bash
ls --help
```

### Using `man` Pages

Access the manual pages for detailed documentation:

```bash
man echo
```
