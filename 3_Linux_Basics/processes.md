# Linux Processes & signals

A process is an instance of a program. It's an independent execution unit with its own resources (CPU, memory, files, network connections, etc.) Processes are managed by the kernel that allocates the dedicated resources. (The kernel is the lowest level of the operating system, between application and hardware). The Linux `ps` command follows the unix `POSIX` standard, which by default, only displays the active processes of the current terminal.

Each process has:

- process ID (`pid`)
- a user under which this process runs under
- a state (`running`, `waiting`, `stopped`, `zombie`)

## Process States

- `R` Running - process is activily using resources (not necessarily open).
- `S` Sleeping - process was running and is now waiting for events / signals. It's not actively using the processor.
- `D` Uninterruptible Sleep - program is waiting for system calls from the kernel (usually during I/O, e.g. opening a file). During that time it does not receive any signals (will be queued for when the process is running again)
- `T` Traced - process has been stopped (paused).Can be invoked by `SIGSTOP`, `SIGTSTP` signals or calling `ptrace`. It can be continued with the signal `SIGCONT`.
- `Z` Zombie - when a process has been executed and needs to be picked up by the parent process (see [Process Endings](#process-endings))
- `I` Idle - Idle kernel threads are
  kernel threads that are currently not doing any work and are waiting to be assigned tasks. This is different from the "S" (Sleeping) state as it specifically applies to kernel threads
  that are in an idle state, not user-space processes waiting for events.

## `ps` command for monitoring processes

The `ps` command stands for "Process Status" and displays information about running processes. It's useful for monitoring system resources.

```bash
ps
# shows all processes of the current terminal (tty)

ps -e (or ps -A)
# shows all processes from all users and from all sessions

ps -f
# full-format listing (extended information, such as user, terminal and parent process (PPID))

ps -l
# shows entries in long format (few more columns)

ps -p 1234,1235
ps 1234 1235
# shows specific processes with ID 1234 and 1235

ps --forest
# shows all processes as an ASCII tree
```

### `ps` command on MacOS

The `ps` command on MacOS is based on the `BSD` unix standard. BSD style parameters are without the `-` dash, e.g. `aux`

```bash
ps -T
# outputs the same as ps on linux

ps aux
# more helpful output of all processes
# a = all users processes
# u = user-friendly output
# x = shows processes without a tty (i.e. processes outside of a terminal)
```

### Niceness

We can change the priority of a process so it does not perform much context switching on the CPU and keeps performing at the expense of other processes requiring CPU resource.
This is done with the `niceness` parameter (a process is nice if it lets other processes interrupt).

- Highest priority (least nice) is `-20`
- Lowest priority (most nice) is `19`
- The default niceness for a new process is `0`

No admin privileges are required for lowering the priority (increasing the niceness) but `sudo` is required for increasing the priority (decreasing the niceness).

```bash
nice -n [niceness] [program]
nice -n 19 gedit
# sets niceness for program gedit to 19 (lowest prio) upon start

renice -n 10 13125
# decreases priority of running process ID 13125

sudo renice -n -10 13125
# increases priority of running process ID 13125
```

Changing a process priority does not mean it runs faster or with better performance. It only means that resources are provided in a more controlled fashion.

## `grep` command to find the ID of a process

```bash
pgrep [program]
# gets pid of main process name

pgrep -f [program]
# gets also sub-processes of that program

renice -n 19 $(pgrep firefox)
# for example setting the priority of the firefox process id
```

## Signals

Signals can be sent to processes to interrupt at a convenient time (whenever a context switch happens). It's a mechanism to asynchronously notify a process of an event. The operating system handles the delivery of the signal to the process as well as queueing multiple signals (signal queue).

Signals can come from different user sources and be of different type:

- `SIGINT` - Interrupt, usually sent from the terminal to a program. It's meant to indicate that the user wants to stop the program and regain control over the terminal. Programs can listen to that signal and perform custom actions - including ignoring this signal (overwriting it). `CTRL`+`C` sends the `SIGINT` signal.
- `SIGHUP` - Hangup, tells the program that the terminal has been closed (will then exit the program). For daemons (background processes) it means it should reload its configuration
- `SIGWINCH` - Window Change
- `SIGTERM` - Terminate Process, tells a process to finish/end clearly (without any data loss) => `Soft`
- `SIGKILL` - Kill Process, forcefully terminates the program **immediately** (might cause data loss) => `Hard`. The `SIGKILL` signal is not handled by the process but by the kernel. Therefore the program isn't even informed about the signal.
- `SIGILL` - Illegal Instruction
- `SIGSTOP` - process is put into a paused state. This signal is not catchable, the process cannot ignore it.
- `SIGCONT` - resumes a stopped process

## `kill` command to send signals to programs

The `kill` command can send any signal to a program (by default it's `SIGTERM`)

```bash
kill -s [SIGNAL] [PID]
kill 12345
# will send SIGTERM to process 12345

kill -l
# lists all signals to send
```

- `kill` as a shell builtin program provides additional functionality and behaves differently than the executable file from the OS (`/usr/bin/kill`)

### `killall` command to send signals to multiple programs

- as opposed to `kill` the signal is sent to the process by name (not ID)

```bash
killall -s [SIGNAL] [PROCESS NAME]

# on MacOS only this syntax will work
killall -SIGNAL [PROCESS NAME]
```

## Process Endings

When a process ends, the resources are made available again.

When a child process terminates, the kernel sends a `SIGCHLD` signal to the parent process. The parent can then retrieve the child's exit status. This is done via a system call (`wait()` or `waitpid()`) to collect the child's exit status. This is known as **process reaping**. In bash, the child's exit code can be access with the variable `$?`.
The parent process is responsible for collecting all child process exit codes. If the parent process ends before the child, child becomes an **orphan process** and is adopted by the init process (`PID 1`).
When a process has finished executing but still has an entry in the process table, it's called a **zombie process**. This usually happens when the parent process has not yet read the child's exit status. However, zombies can lead to process table overflow, possibly restricting new processes to be started.
We can find out the maximum number of processes in `/proc/sys/kernel/pid_max`. They are marked as `Z` in the process table (`ps -l`). When the parent process ends, the zombie process is usually removed automatically. If for whatever reason, the parent process didn't pick it up, we can send a `SIGCHLD` signal to the parent or we could kill the parent process and then the init process will adopt it and reap it.

**Exit Codes**

- `0` exited successfully
- `1` exited with error

## `top` command to monitor system activity

The `top` program shows all processes from a system. It looks in the folder `/proc/`, which contains relevant files of process IDs. The `top` programm emulates a visual interface for this file system of process IDs. Launching `top` as a normal user lists different tasks as launching `sudo top` with root access (full overview). The **load average** means how much processing power (CPU cores) have been used in the past (1-15 minutes)

**Options**

- `-u [username]`: shows only processes owned by the specified user
- `-d [seconds]`: sets the delay between display updates (default is 3 sec)
- `-i`: starts `top` with idle processes hidden. This shows only processes that are currently using CPU resources
- `-c`: Displays the full command line used to start each process, instead of just the command name in the process name column

While `top` is running:

- `f key`: to further customize the output (e.g. sorting, enable certain columns)
- `k key`: to kill processes (sends signals)
- `r key`: to change niceness of a process
- `z key`: to switch to color mode and `Z key` to configure it
- `W key`: to save current configuration, so it's loaded with next startup (user specific)

## `htop` as a better alternative to `top`

Works the same as `top` program but in addition has a more visual appeal.
