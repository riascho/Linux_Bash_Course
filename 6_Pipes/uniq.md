# Uniq Command

## Overview

The `uniq` program removes duplicate lines from input.

## Important Note

⚠️ **Input must be sorted first!** `uniq` only removes duplicate lines that are **directly adjacent** to each other.

## Basic Usage

### Remove Duplicates from Sorted Data

```bash
cat users.txt | sort | uniq
```

This pipes the file through `sort` first, then applies `uniq` to return only unique lines.

## Options

### `-d` - Display Duplicates

```bash
uniq -d
```

Shows only the duplicate entries that were removed (instead of showing unique lines).
