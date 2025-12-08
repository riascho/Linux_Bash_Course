# Data Streams

The redirect characters in Bash, `>` and `<` (and their variations), are used to change where a command gets its input and where it sends its output.

## Overview: Three Standard Streams

Bash has 3 standard streams:

- **0 - stdin** (standard input)
- **1 - stdout** (standard output)
- **2 - stderr** (standard error)

## Output Redirection (stdout)

### Basic Redirection with `>`

Write output to a file (overwrites existing content):

```bash
echo 'Hello Bash!' > [filepath]
```

### Appending with `>>`

Append new content to a file:

```bash
du -h [file] >> [filepath]
```

**Note:** `>` and `>>` only work on the standard output stream (`stdout`) by default.

## Error Redirection (stderr)

### Understanding Stream Numbers

- `>` or `>>` default to stdout (1) without a parameter
- They are equivalent to `1>` and `1>>`
- To redirect stderr, specify stream 2: `2>` or `2>>`

### Example: Test stderr

Try causing an `stderr` error with the `du` command by referencing a file that doesn't exist.

### Redirecting stdout and stderr Separately

```bash
du -h [file] > output.txt 2> error.txt
```

### Discarding Error Output

Redirect errors to `/dev/null` to ignore error outputs:

```bash
[command] 2> /dev/null
```

**Note:** `/dev/null` is a special file that discards all data written to it.

### Redirecting stderr to stdout

Combine both streams (order matters!):

```bash
[command] > out.txt 2>&1
```

**Important:** The order is crucial! stdout must be defined first before redirecting stderr to it. Before `> out.txt`, stdout is still the terminal.

## Using the `tee` Command

The `tee` command writes to both a file and stdout simultaneously:

```bash
ping yahoo.com 2>&1 | tee ping.txt
```

This redirects stderr to stdout (terminal) and pipes everything to both the terminal and `ping.txt`.

## Input Redirection (stdin)

### Interactive Input

Many commands accept stdin when no file is provided:

```bash
wc -l
[write your input]
# Press Ctrl + D to quit
# Output will be printed
```

The `cat` program also accepts stdin by default if no file parameter is provided.

### Redirecting stdin from a File

Use `<` to redirect stdin from a file:

```bash
[command] < in.txt
```

### Combining Input and Output Redirection

Stream input from one file to another (essentially copying):

```bash
[command] < in.txt > out.txt
```

🔁 Redirection CharactersIn Unix-like systems, every running process typically has three standard data streams, each associated with a numbered file descriptor:0: Standard Input (stdin) - Where a command reads its input (usually the keyboard).1: Standard Output (stdout) - Where a command writes its normal output (usually the terminal).2: Standard Error (stderr) - Where a command writes its error messages (usually the terminal).The redirection characters manipulate these file descriptors:1CharacterDescriptionExample>Redirects stdout (FD 1) to a file, overwriting the file if it exists.ls > file_list.txt>>Redirects stdout (FD 1) to a file, appending to the file if it exists.date >> log.txt<Redirects stdin (FD 0) to read input from a file instead of the keyboard.sort < data.txt2>Redirects stderr (FD 2) to a file, overwriting it.command 2> errors.log&>Redirects both stdout and stderr to a file (Bash extension).command &> all_output.log2>&1Redirects stderr (FD 2) to the same location as stdout (FD 1).command > file 2>&1🔄
