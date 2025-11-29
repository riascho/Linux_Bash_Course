# PATH Environment Variable

## Overview

The `PATH` environment variable stores a list of directories, separated by colons (`:`), which are searched for executable programs **from left to right**.

## Finding Program Paths

### Using `command -v`

```bash
command -v [program_name]
```

Asks the shell how it would resolve the given program name.

**Breakdown:**

- `command` - A POSIX shell builtin that controls lookup/exec behavior
- `-v` - Query mode. Prints the path if it's an external executable; otherwise prints the word itself if it's a builtin/function/alias

### Using `which`

```bash
which [program_name]
```

Simpler alternative to find the path of a program.

## Adding Directories to PATH

Simply append the new directory with a colon separator:

```bash
PATH="${PATH}:/new/path"
```

## Making Python Files Executable

See: [hello.py](./hello.py)

### Step 1: Add Shebang

Add this as the first line to specify which Python environment should run the program:

```bash
#!/usr/bin/env python3
```

### Step 2: Make File Executable

The file can then be executed by running:

```bash
[filename]
```

**From anywhere:** If the directory is added to `PATH`  
**From specific directory:** Navigate to the directory first

### Equivalent to:

```bash
python3 [filename]
```
