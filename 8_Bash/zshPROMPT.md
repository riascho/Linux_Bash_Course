# Configuring PS1 in Zsh

## Overview

In `Zsh`, the prompt variable is called `PROMPT` (though `PS1` is an alias to it). The syntax is different from Bash!

## Zsh Prompt Escape Sequences (Different from Bash!)

| Zsh          | Bash      | Meaning                                        |
| ------------ | --------- | ---------------------------------------------- |
| `%n`         | `\u`      | Username                                       |
| `%m`         | `\h`      | Short hostname                                 |
| `%M`         | `\H`      | Full hostname                                  |
| `%~`         | `\w`      | Current directory with ~                       |
| `%c` or `%C` | `\W`      | Directory basename                             |
| `%T`         | `\t`      | Time (24-hour HH:MM)                           |
| `%*`         | -         | Time (24-hour HH:MM:SS)                        |
| `%D`         | `\d`      | Date (yy-mm-dd)                                |
| `%#`         | `\$`      | # if root, % otherwise                         |
| `%(?:T:F)`   | -         | Ternary based on exit status                   |
| `%{...%}`    | `\[...\]` | Non-printing sequences                         |
| new line     | `\n`      | no new line character, need a literal new line |

## Zsh built-in color codes

```zsh
%F{color}text%f    # Foreground color
%K{color}text%k    # Background color
```

- Color names: black, red, green, yellow, blue, magenta, cyan, white
- You can use color names or numbers (0-255)

```zsh
PROMPT="%F{green}%n@%m%f:%F{yellow}%~%f%# "
# Or with numbers:
PROMPT="%F{82}%n@%m%f:%F{226}%~%f%# "
```

Too see all colors available, run:

```zsh
for i in {0..255}; do print -Pn "%K{$i}  %k %F{$i}${(l:3::0:)i}%f " ${${(M)$((i%6)):#3}:+$'\n'}; done
```

## Formatting

`%B` - bold | `%b` - reset bold
`%U` - underlined | `%u` - reset underlined
`%f` - reset forground | `%k` - reset background

## Example

```zsh
PROMPT="%U%F{82}%n@%M:%f%k%F{yellow}%~%F{cyan}%u %D{%a %d %b} %*%F{green}%B
%{$(tput blink)%}→%b%f "
```
