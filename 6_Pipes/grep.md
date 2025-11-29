# Grep Command

## Overview

`grep` is a powerful tool used to search for patterns in text output or files. It prints entire lines where the pattern is found.

## Basic Syntax

### Search in Files

```bash
grep -F [pattern] [filepath]
```

The `-F` flag disables regex pattern matching and treats the pattern as a literal string (Filter mode).

### Search from stdin

```bash
[command] | grep -F [pattern]
```

## Examples

### Filter Files by Extension

```bash
ls | grep -F 'txt'
```

Filters for any `.txt` files in the current directory.

## Output Behavior

`grep` prints **whole lines** where the search string was found, not just the matching text.
