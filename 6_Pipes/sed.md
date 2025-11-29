# Sed Command (Stream Editor)

## Overview

`sed` is a powerful stream editor used for text manipulation and transformation.

## Basic Syntax for Substitution

```bash
sed 's/[pattern]/[replacement]/[flags]'
```

- `s` stands for **substitute**

## Flags

- `g` - **Global**: substitute all occurrences in each line (not just the first match)

## Example

### Basic Substitution

```bash
echo 'Hello world!' | sed 's/world/bash/g'
```

**Output:** `Hello bash!`

The `g` flag ensures all occurrences of "world" are replaced with "bash".
