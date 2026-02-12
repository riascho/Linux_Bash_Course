- to allow a file to be executable: `chmod +x [file name]`

- using `ping` to communicate with a server (handy to check internet connection etc.)

- `ip addr show` - shows IP address info (only linux)
  `ifconfig -a` - for Mac

- `uptime` is a command that shows how long a system has been running

- use the `unzip` command to unpack zip files:
  `unzip archive.zip -d [destination for extraction]`
  `-d` sets the destination directory, otherwise the zip file would be extracted in current working directory

- the `watch` command is used to automatically re-execute a command.

```bash
watch -n 0.5 grep ctxt /proc/12345/status
# refresh every 0.5 seconds to get the context switches of process 12345
```

- `cmatrix` fun little programm that turns the terminal into matrix

## programs

There is 2 types of programs:

- built-in shell programs
- executable file

- `type [program name]` tells which type it is (and path)

## `stty`

The `stty` command is a tool to change the terminal line settings

```bash
stty tostop
# turns on that any program is run until it produces output. It is then being stopped (paused)

stty -tostop
# turns this feature back off
```
