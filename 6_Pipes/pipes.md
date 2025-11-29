# Pipes

## Overview

Pipes (`|`) take the output of one command and use it as input for the next command, allowing you to chain multiple commands together.

## Basic Syntax

```bash
[command] | [command] | ... | [final command]
```

## The Tee Utility

### Purpose

Instead of only streaming output to a file using `>`, the `tee` utility creates **dual output** - displaying in the terminal AND saving to a file simultaneously.

### Syntax

```bash
[command] | tee [options] [filename]
```

### Options

- `-a` - Append to file (similar to `>>`)

### Examples

**Append to file:**

```bash
echo '------' | tee -a hello.txt
```

**Chain with other pipes:**

```bash
echo '------' | tee hello.txt | wc -c
```

This preserves the output for the next pipe while also saving it to a file.
