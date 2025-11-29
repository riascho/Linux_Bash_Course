# PS1 (Primary Prompt String)

## Overview

`PS1` is Bash's "primary prompt string" — the template Bash expands and prints each time it's ready for a new command. It defines the appreance of the primary shell prompt (enhances user experience and workflow efficiency). It can be set like any variable; exporting passes it to subshells.

## Basic Example

```bash
export PS1='\[\e[32m\]\w\[\e[0m\]\$ '
```

### Breaking Down the Example

| Component        | Purpose                                                            |
| ---------------- | ------------------------------------------------------------------ |
| `export PS1=...` | Set (and export) the prompt template                               |
| `\[` ... `\]`    | Mark non-printing sequences so line editing/wrapping stays correct |
| `\e[32m`         | ANSI escape "set foreground color to green"                        |
| `\w`             | Current working directory (with `~` for `$HOME`)                   |
| `\e[0m`          | Reset colors/attributes                                            |
| `\$`             | Shows `#` if root, otherwise `$`                                   |
| trailing space   | Visual separation before your typing                               |

## Why Single Quotes?

Single quotes preserve backslashes so Bash can interpret prompt escapes at display time.

**Example:** In double quotes `\$` would turn into a literal `$`, breaking the special `\$`. Single quotes keep it as `\$`.

## Common PS1 Escape Sequences

### User & Host Information

- `\u` - Username
- `\h` - Short hostname
- `\H` - Full hostname

### Directory Information

- `\w` - Current working directory (with `~`)
- `\W` - Basename of current working directory

### Time & Date

- `\t` - 24-hour time (HH:MM:SS)
- `\A` - 24-hour time (HH:MM)
- `\@` - 12-hour time
- `\d` - Date

### Special Characters

- `\n` - Newline
- `\!` - History number
- `\#` - Command count
- `\j` - Number of jobs
- `\\` - Literal backslash
- `\$` - `#` for root, `$` otherwise

### Formatting (using escape sequences)

- `\[\e[1m\]` - bold
- `\[\e[5m\]` - blinking
- `\[\e[93m\]` - forground color
- `\[\e[32;40m\]` - foreground ; background color
- `\[\e[0m\]` - color reset
- `\[\e(b\e[m\]` - reset all formatting

**IMPORTANT:** Wrap escape sequences in `\[...\]` so the terminal does not count them as normal characters (also applies to `tput)

**My favorite**:

```bash
PS1='
\[$(tput smul)\]
\[$(tput setab 5)\]
\[\e[92m\]
\u@\H:
\[\e[0;0m\]
\[\e[93m\]
\w
\[\e[96m\]
\[$(tput rmul)\]
| \d \t\n
\[\e[92m\]
\[\e[1m\]
\[\e[5m\]
→
\[\e[0m\] '
```

(This is just for better visualization! Make sure to remove the line breaks!)
