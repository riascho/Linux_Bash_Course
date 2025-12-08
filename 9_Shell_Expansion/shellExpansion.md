# Shell Expansions

Before executing, the shell "rewrites" and parses the command.

`*` - expands all filenames, e.g. `ls *`
`~` - expands to home directory (`HOME` var)
`~+` - pwd
`$...` / `${...}` / `"${...}"` - variable expansion, use with double quotes and curly braces !

- curly braces define where the env var ends
- double quotes

## Shell Parameter Expansion

Shell parameter expansion allows you to manipulate variables and strings directly in the shell without needing external commands. Use these when you need to transform, extract, or query variable values efficiently.

### Length of String

Get the length of a variable's value.

```bash
${#variable}
```

**Example:**

```bash
NAME="Maria"
echo ${#NAME}  # Output: 5
echo ${#HOME}  # Output: length of home directory path
```

### Substring Extraction

Extract a portion of a string starting at a position with optional length.

```bash
${variable:start:length}
```

**Examples:**

```bash
PATH="/usr/local/bin"
echo ${PATH:0:4}    # Output: /usr (start at 0, length 4)
echo ${PATH:5}      # Output: local/bin (start at 5, rest of string)
echo ${PATH:5:5}    # Output: local (start at 5, length 5)
echo ${PATH: -3}    # Output: bin (last 3 characters, note the space before -)
```

### Variable String Replacement

Replace patterns within a variable value.

**Single occurrence (first match only):**

```bash
${variable/pattern/replacement}
```

**Example:**

```bash
FILE="report_2023_final_2023.txt"
echo ${FILE/2023/2024}  # Output: report_2024_final_2023.txt
```

**All occurrences:**

```bash
${variable//pattern/replacement}
```

**Example:**

```bash
FILE="report_2023_final_2023.txt"
echo ${FILE//2023/2024}  # Output: report_2024_final_2024.txt
```

**Remove pattern (omit replacement):**

```bash
TEXT="Hello World"
echo ${TEXT/ World}     # Output: Hello (removes " World")
echo ${TEXT//l}         # Output: Heo Word (removes all "l")
```

### Default Values

Use default values when variables are unset or empty.

```bash
${variable:-default}    # Use default if variable is unset or empty
${variable:=default}    # Use default AND assign it to variable if unset/empty
${variable:?error_msg}  # Display error message if variable is unset or empty
${variable:+value}      # Use value only if variable is set
```

**Examples:**

```bash
echo ${UNDEFINED:-"default value"}  # Output: default value
echo ${NAME:-"Guest"}               # Output: Maria (if NAME is set)

# Assign default
echo ${NEW_VAR:="assigned"}         # Output: assigned (and NEW_VAR is now set)
echo $NEW_VAR                       # Output: assigned

# Error handling
echo ${REQUIRED:?"Variable must be set"}  # Shows error if REQUIRED is unset
```

### Prefix/Suffix Removal

**Remove shortest match from beginning:**

```bash
${variable#pattern}
```

**Remove longest match from beginning:**

```bash
${variable##pattern}
```

**Remove shortest match from end:**

```bash
${variable%pattern}
```

**Remove longest match from end:**

```bash
${variable%%pattern}
```

**Examples:**

```bash
FILENAME="archive.tar.gz"
echo ${FILENAME#*.}     # Output: tar.gz (removes shortest: "archive.")
echo ${FILENAME##*.}    # Output: gz (removes longest: "archive.tar.")
echo ${FILENAME%.*}     # Output: archive.tar (removes shortest: ".gz")
echo ${FILENAME%%.*}    # Output: archive (removes longest: ".tar.gz")

PATH_VAR="/usr/local/bin/python"
echo ${PATH_VAR##*/}    # Output: python (gets basename)
echo ${PATH_VAR%/*}     # Output: /usr/local/bin (gets dirname)
```

### Case Modification

```bash
${variable^}    # Capitalize first character
${variable^^}   # Convert to uppercase
${variable,}    # Lowercase first character
${variable,,}   # Convert to lowercase
```

**Examples:**

```bash
NAME="maria smith"
echo ${NAME^}       # Output: Maria smith
echo ${NAME^^}      # Output: MARIA SMITH

UPPER="HELLO"
echo ${UPPER,}      # Output: hELLO
echo ${UPPER,,}     # Output: hello
```

### When to Use Parameter Expansion

- **Performance**: Faster than calling external commands like `cut`, `sed`, or `awk` for simple operations
- **Portability**: Works in all POSIX-compliant shells (bash, zsh, etc.)
- **Simplicity**: Clean, readable syntax for common string operations
- **Scripts**: Essential for robust shell scripts that handle variables safely
- **No dependencies**: Doesn't require external tools or utilities

## Word Splitting

Bash performs word splitting on our input after shell expansions have been parsed.
e.g. this command `touch a.txt b.txt` is 3 words

How words are split is defined in the `IFS` variable. By default, it's at every space, tab and new line. A sequence of these characters acts as a single delimiter, e.g. `touch    a.txt    b.txt` is being split the same as above.

To view this variable we can use the program `hexdump` to see the hexadecimal representation of these hidden characters

```bash
echo "${IFS} | hexdump
```

Word Splitting can be disabled by using (any) quotes (to include spaces etc. in a word), e.g. `touch 'a file.txt'`

## Quotes

- no quotes: all available shell expansions as well as word splitting are being applied

```bash
echo $PWD/*.txt
```

- single quotes: all expansions as well as word splitting are disabled

```bash
echo '$PWD/*.txt'
```

- double quotes: only variable and parameter expansions are enabled, filename expansion and word splitting are disabled

```bash
echo "$PWD/*.txt"
```

> Quotes do not operate for strings in bash, like in other programming languages!
> Bash will remove quotes where not necessary

## Expansion Pitfalls

**The Bash Process**

1. Command is being expanded
2. Word Splitting applied
3. Quotes are removed
4. Command is being executed

**Best Practices**

- refer to filenames in the same directory with `./` or use single quotes `'`
- always use the quoting style that is as restrictive as possible
- use double quotes `"` (and `{}`) when working with variables

e.g. if pwd = 'a folder' (with space!)

```bash
→ touch $PWD/file.txt
# won't work

→ touch "${PWD}/file.txt"
# will work
```

> The double quotes prevent word splitting, and hence the 'a folder' is kept as a whole directory

## Escaping

Escaping is done to disable Bash from performing default actions with the a backslash `\`

e.g. navigating into a folder with space

```bash
cd a\ folder
cd 'a folder' # same
```

> however, single quotes disable the backslash escape! i.e. `'\''` will just remove the single quotes and leaves an unfinished open single quote

## Brace Expansion

**Brace expansion** generates multiple strings from a single expression by expanding text within curly braces `{}`. It's useful for creating sequences, alternatives, or combinations without repetition.

The shell expands `{a,b,c}` into separate arguments: `a`, `b`, `c`. You can also use ranges like `{1..5}` for numeric or alphabetic sequences `{a..z}`/`{A..Z}`

**Examples:**

```bash
# Generated by Copilot
# Create multiple files at once
touch file_{1,2,3}.txt
# Expands to: touch file_1.txt file_2.txt file_3.txt

# Numeric range
echo {10..15}
# Output: 10 11 12 13 14 15

# Nested braces
mkdir -p project/{src,tests,docs}
# Creates: project/src, project/tests, project/docs

# Combination
echo {a,b}{1,2}
# Output: a1 a2 b1 b2
```

> brace expansion **does not** work in quotes!

## Command Substitution

To execute a command and use the output as a replacement, we can use `$(...)` or backticks \`` (not recommended). This will execute the command in a subshell environment and collect the output.

> prefer to use command substitution in double quotes in order to avoid word splitting

## Process Substitution

Process substitution allows the output of a command (or group of commands) to be treated as a temporary file or a stream that can be read by another command. It avoids the need for explicitly creating temporary files.

**Syntax**:

1. `>(command)`: The output from the main command is written to the command inside the substitution, treating it as an output file.
2. `<(command)`: The output of command is treated as an input file.

**How it Works**

- When Bash encounters a process substitution, it executes the command(s) inside the parentheses asynchronously (in the background).
- It creates a named pipe (`FIFO`) or uses a special file path like `/dev/fd/N` (where `N` is a file descriptor number).
- The entire process substitution expression is then replaced with the path to this temporary "file-like" object on the command line before the main command executes.
- The main command treats this path as a regular filename, reading from or writing to the pipe, which is connected to the substituted process.

**Common Use Case**

1.

- The most common use is when a command, like `diff`, expects multiple filenames as arguments, but you want to compare the output of two different commands.

```bash
diff <(ls ./folder1) <(ls ./folder2)
```

In this example, the `diff` command receives two temporary filenames as arguments. When `diff` reads from these filenames, it is actually reading the content from the two separate `ls` commands running in the background.

2.

```bash
wc -l < <(ls)
```

using redirect `<` the output of `ls` will be input for the `wc` program
`wc -l` is executed at the same time, but waiting for input because of `<`

3.

```bash
echo "test" > >(cat)
```

the echo command and the cat command are both being executed asynchronously in subshells
the `cat` command is waiting for input (from temporary file) though before it can execute
