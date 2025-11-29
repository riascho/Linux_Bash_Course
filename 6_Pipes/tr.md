# Tr Command (Translate)

## Overview

The `tr` program replaces or deletes characters from stdin. It works on **single characters** at a time.

## Basic Syntax

```bash
tr [options] [set1] [set2]
```

## Usage Examples

### Character Replacement

```bash
echo 'bash' | tr 'ba' 'z'
```

**Output:** `zzsh`

Both 'b' and 'a' are replaced with 'z' (character-by-character mapping).

### Pattern-Based Replacement (Ranges)

```bash
echo 'awesome' | tr 'a-z' 'A-Z'
```

**Output:** `AWESOME`

Capitalizes all characters from a to z.

### Replace with Single Character

```bash
echo 'awesome' | tr 'a-z' '.'
```

**Output:** `.......`

All lowercase letters are replaced with dots.

### Delete Characters

```bash
echo 'bash is awesome' | tr -d ' '
```

**Output:** `bashisawesome`

The `-d` flag deletes all specified characters (in this case, spaces).
