# Environment Variables

## Naming Convention

Environment variables are by convention written in **UPPER CASE**.

## Viewing Environment Variables

### List All Variables

```bash
env
```

Lists all available environment variables in the current shell.

### View a Single Variable

```bash
echo "$PWD"
```

or (safest):

```bash
echo "${PWD}"
```

### String Interpolation

Use curly braces when the command contains additional text:

```bash
echo "${PATH} something something"
```

### Important Note

⚠️ **Always use quotes** when referencing variables:

```bash
echo "$PATH"  # Good
echo $PATH    # Avoid - shell expansion may cause unexpected behavior
```

## Setting Environment Variables

### Create/Export a Variable

```bash
export VAR=value
```

**Important:** No spacing around the `=` sign!

### Quote Usage

- Use **single quotes** for literal strings (prevents shell expansion)
- Use **double quotes** for shell expansion

### Overwrite a Variable

```bash
VAR='new value'
```

No whitespace around the `=` sign!

### Delete a Variable

```bash
unset VARNAME
```

## Temporary Environment Variables

Set an environment variable only for a specific program execution:

```bash
CONFIG='localhost:3306' python hello_world.py
```

**Syntax:**

```bash
[VAR]=[value] [program] [filename]
```

The variable is only available during that single command execution.
