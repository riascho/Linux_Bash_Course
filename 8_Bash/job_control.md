# Jobs

A **Job** is a command that is being executed. A job therefore is started via a command. A job can consist out of multiple programs, e.g. `cat file.txt | wc` (two processes but one job).

A **Foreground Job** occupies the shell, i.e. bash will wait for its completion before accepting a new command. Only foreground jobs can receive keyboard input. This also includes signals, such as `SIGINT` (`ctrl`+`c`).

A **Background Job** is running in the background and other commands can be used in the same terminal.

To start a background job use the `&`: `[command] &`. Output will still be displayed in our shell, unless we redirect it, together with the job ID and process ID (e.g. `[1] 15225`)

```bash
ping -c 10 google.com > ping.txt &
# background job with output redirection
```

## Managing Jobs

Background jobs cannot receive keyboard input. For this, they will need to be brought back to the forground (and made foreground jobs)

```bash
jobs
# will print all jobs that are currently running

fg %[job ID]
# brings job to the foreground (active in terminal)

`CTRL + Z
# suspends/pauses job in the foreground (moves it to background)

fg %[job ID]
# resumes job in the foreground
bg %[job ID]
# resumes a job in the background
# (sends SIGCONT signal to program)

kill -s [signal] %[job ID]
# sends signal to job (to kill a job for example)
```

## `wait` command to wait for background jobs

With the `wait` command, we can wait for background jobs. It launches and then tracks all jobs and returns output when they're finished. This is more interesting for bash scripting.

```bash
wait
# waits until ALL currently running background jobs have changed their state

wait [process ID]
wait %[job ID]
# waits for specific job/process

wait -n
# waits for any job to be completed
```

e.g.
Start several ping jobs, then run `wait; tput bel; echo 'Pings complete!'`

## `nohup` command to keep background jobs running

We can use the `nohup` command to launch a program that will remain open even if we close our terminal or log out. It basically launches the program with the `SIGHUP` signal disabled.
The `stdout` will be redirected into a `nohup.out` file in the current directory (or home directory).

For example

```bash
nohup ping -c 100 google.com
# SIGHUP disabled - process continues after terminal closes, but runs in foreground (can receive SIGTERM signal)

ping -c 100 google.com &
# runs in background but will terminate on SIGHUP (terminal close)

nohup ping -c 100 google.com &
# SIGHUP AND SIGTERM disabled
```
