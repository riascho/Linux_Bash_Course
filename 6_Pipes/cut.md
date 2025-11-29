# Cut Command

## Overview

The `cut` command is used to process and extract data from a file or stdin by selecting specific portions of each line.

## Usage Options

### Cutting by Byte

Extract a specific range of bytes from each line.

**Syntax:**

```bash
cut -b <range>
```

**Example:**

```bash
uptime | cut -b 1-10
```

Extracts bytes 1 through 10 from the output.

### Cutting by Characters

Extract specific characters from each line.

**Syntax:**

```bash
cut -c <range>
```

**Example:**

```bash
uptime | cut -c 2-
```

Extracts from the 2nd character until the end of the line.

### Cutting by Fields

Extract fields separated by a delimiter. This is particularly handy for selecting words.

**Syntax:**

```bash
cut -d '<delimiter>' -f <field_number>
```

**Example:**

```bash
echo 'Hello World!' | cut -d ' ' -f 2
```

**Output:** `World!`

This uses space as the delimiter and selects the 2nd field.
