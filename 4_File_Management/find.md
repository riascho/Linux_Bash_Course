# Find Command

## Overview

`find` is an executable program in Unix that can search for files, nested directories, and hidden files.

## Basic Syntax

```bash
find [directory] -type [filetype] -mtime -7 -size +10M
```

## Common Options

### File Types (`-type`)

- `f` - regular files
- `d` - directories

### Modification Time (`-mtime`)

- Filters by modification timestamp in days
- Example: `-mtime -7` - files modified in the last 7 days

### File Size (`-size`)

- Filters by file size (in Bytes)
- Example: `+10M` - files bigger than 10MB
- Example: `-5M` - files smaller than 5MB

### Delete Flag (`-delete`)

- **Warning**: Deletes all files matching the search criteria
- Use with caution!

## Tips

- Press `Ctrl + C` to stop the find process
- Use `man find` to open the full documentation

## Examples

```bash
# Find all files modified in the last 7 days
find . -type f -mtime -7

# Find large files (>10MB)
find . -type f -size +10M

# Find and delete old log files
find /var/log -type f -name "*.log" -mtime +30 -delete
```
