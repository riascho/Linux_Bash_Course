# Globbing

## Overview

**Globbing** refers to bash/shell filename expansions, which allows you to write commands in a shortcut way using wildcard characters. The shell automatically expands these patterns before executing the command.

### Common Use Cases

- Searching for files
- Moving multiple files at once
- Operating on groups of files matching a pattern

> **Note:** Globbing does NOT use Regex! It has a different syntax.

## Wildcard Patterns

### Single Character Matching

| Pattern | Description                  | Example                                      |
| ------- | ---------------------------- | -------------------------------------------- |
| `?`     | Matches any single character | `file?.txt` matches `file1.txt`, `fileA.txt` |

### Multiple Character Matching

| Pattern | Description                                 | Example                                               |
| ------- | ------------------------------------------- | ----------------------------------------------------- |
| `*`     | Matches any single or multiple character(s) | `*.txt` matches all `.txt` files                      |
| `**/`   | Matches any folders and nested directories  | `**/*.js` matches all `.js` files in any subdirectory |

### Character Ranges

| Pattern | Description                                 | Example                                                       |
| ------- | ------------------------------------------- | ------------------------------------------------------------- |
| `[0-9]` | Matches any one number (0-9)                | `file[0-9].txt` matches `file0.txt` through `file9.txt`       |
| `[a-z]` | Matches any one lowercase letter (a-z)      | `[a-z]*.txt` matches files starting with any lowercase letter |
| `[A-Z]` | Matches any one uppercase letter (A-Z)      | `[A-Z]*.txt` matches files starting with any uppercase letter |
| `[abc]` | Matches any one of the specified characters | `file[abc].txt` matches `filea.txt`, `fileb.txt`, `filec.txt` |

## Examples

```bash
# List all text files
ls *.txt

# Remove all files starting with 'temp'
rm temp*

# Copy all .jpg and .png files
cp *.{jpg,png} /destination/

# Find files with single character names
ls ?.txt

# Match files with numbers in name
ls file[0-9].txt
```
