# File Management

## Creating Files and Directories

### `touch` - Create Files

```bash
touch [filename]
```

- Creates a new empty file
- If used with an existing file, it updates the timestamp

### `mkdir` - Create Directories

```bash
mkdir [directory_name]
```

- Creates a new folder/directory

---

## Listing Files

### `ls` - List Directory Contents

```bash
ls -l
```

- `-l` flag shows additional information (permissions, owner, size, date)

### `tree` - Display Directory Structure

```bash
tree [directory]
```

- Lists all files in a nice tree structure showing the hierarchy

---

## Moving and Renaming

### `mv` - Move/Rename Files

```bash
mv [filename] [destination_path]
```

- Moves files or directories to a new location
- Also used for renaming: `mv [old_name] [new_name]`
- Can combine moving and renaming in one command

**Moving Multiple Files:**

```bash
mv *.png ../Desktop
```

- Uses wildcard characters to move multiple files at once

---

## Copying Files

### `cp` - Copy Files/Directories

```bash
cp -R [source] [destination]
```

- Copies files or folders
- `-R` flag: copies recursively (folder with all contents)
- **Important:** The last argument is always the destination when copying multiple files

---

## Deleting Files and Directories

### `rm` - Remove Files/Directories

```bash
rm [filename]
rm -r [directory_name]
```

- Removes files or directories
- `-r` flag: recursive deletion (for directories with contents)
- `-f` flag: force deletion without prompting
- `-rf` together: recursive + force (deletes nested directories without asking)

⚠️ **Warning:** Be careful with `rm -rf` - it's powerful and irreversible!

### `rmdir` - Remove Empty Directories

```bash
rmdir [directory_name]
```

- Only deletes empty directories (safer option)
- Still considers hidden files, so check for those first

---

## Wildcard Safety Tips

**DO:**

```bash
echo ./*
```

**DON'T:**

```bash
echo *
```

- Using wildcards with relative paths (like `./`) is more secure
- Prevents bash expansion from reading filenames as commands (e.g., a file named `-rf`)

---

## Viewing File Contents

### `cat` - Print Entire File

```bash
cat [filepath]
```

- Prints all content of a file to the terminal

- use the flag `-v` for visual display (safer option, in case of any shell scripts that could be in a file)

### `head` - View Beginning of File

```bash
head -n 10 [filepath]
```

- Prints the first n lines of a file

### `tail` - View End of File

```bash
tail -n 10 [filepath]
```

- Prints the last n lines of a file

### `less` - Interactive File Viewer

```bash
less [filepath]
```

- Opens file in terminal for scrolling
- **Navigation:**
  - Arrow keys: scroll line by line
  - `f` / `b`: scroll page by page (forward/backward)
  - `50p`: jump to 50% of the content
  - `=`: show details about current progress
- **Search:**
  - `/[word]`: forward search (case-sensitive)
  - `?[word]`: backward search (case-sensitive)
- **Options:**
  - `-N`: toggle line numbers on/off
  - `q`: quit the program

---

## File Information

### `wc` - Word Count

```bash
wc -lwc [filepath]
```

- Gets metadata about a file
- **Flags:**
  - `-l`: number of lines
  - `-w`: number of words
  - `-c`: number of bytes

### `du` - Disk Usage

```bash
du [filepath]
```

- Shows file or directory size
- **Flags:**
  - `-s`: summary only (just the total)
  - `-h`: human-readable format (KB, MB, GB)

---

## Editing Files

### `nano` - Text Editor

```bash
nano [filepath]
```

- Opens file in the nano text editor
- Creates the file if it doesn't exist
- Built-in commands shown at bottom of screen
