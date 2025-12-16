# Files on Unix systems

Everything is considered a **file** in Linux ("Unix philosophy").

Different types of files:

- ordinary file `-`
- directories `d`
- symbolic links ("symlinks") `l`
- character device `c`
- block device `b`
- named pipes `p`
- sockets `s`

show type of a file:

```bash
ls -l [folder / file]
# output will be the first character (d)
## e.g. drwxr-xr-x   7 ria  staff   224 Dec  3 17:03 a folder
```

to include hidden files:

```bash
ls -la [folder / file]
```

## Symlinks (Symbolic Links)

this special kind of file serves as reference to another file (or directory). It's a special way of "shortcut" to another destination. This reference to another file is being resolved with the same access rights of the symlink (affects read & write operations). Common use cases include creating convenient access points to frequently used directories, maintaining multiple references to the same file, or linking to files in different locations without duplicating data.

- to create a symlink, using the `ln` programm:

```bash
ln -s [target] [link name]
# e.g.
ln -s Desktop/ linkToDesktop
# can now be navigated to
cd linkToDesktop
```

both paths are individual but being synced
