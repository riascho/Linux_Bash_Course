# Sort Command

## Overview

The `sort` program sorts the contents of a file or stdin. Default sorting is **alphabetical**.

## Common Options

### `-r` - Reverse Order

```bash
sort -r
```

Sorts in reverse order.

### `-n` - Numerical Sort

```bash
sort -n
```

Sorts numbers in numerical order (instead of lexicographically).

### `-c` - Check if Sorted

```bash
sort -c
```

Checks if contents are already sorted and identifies unsorted elements.

### `-k` - Sort by Column

```bash
sort -k [column_number]
```

Sorts data by a specific column (1-indexed). Useful for CSV or space-delimited files.

### `-u` - Unique Lines

```bash
sort -r -u
```

Removes duplicate lines (`-u` stands for unique).

## Important Behavior Difference

### Direct File Modification

```bash
sort [filename]
```

When given a file directly, `sort` changes the file in place.

### Using stdin (No Modification)

```bash
cat [filename] | sort
```

When using stdin, `sort` prints the sorted result to stdout without modifying the original file.
