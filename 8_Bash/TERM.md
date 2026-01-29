- terminals have different abilities
- we can tell bash which abilities should be used by the environment with `TERM`
- e.g. there is no text-only terminals, or `xterm-256color`
- `toe` lists available modes (table of terminfo entries)
- `infocmp` lists terminal's capabilities (supported escape sequences)
- we can use `tput` to use shortcuts for escape sequences instead (e.g. `sgr0` instead of `\e(b\e[m` for default reset)

## Terminal Escape Sequences

**Example:**

```bash
echo -e "\e[32;40m"
```

- `\e` Escape Character tells the terminal that what follows is an escape sequence
- `[` - indicates terminal changes
- `[32` - green foreground color
- `;` separation for background color
- `40m` - black background
- `\e[0m\]` - resets color
- `\e(b\e[m` - resets all formatting
