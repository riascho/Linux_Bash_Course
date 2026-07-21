# System Boot Process

When a system is started, the bootloader is the first software to run on startup. Its goal is to load the kernel (operating system) into memory and then hands control to it. Usually this process happens very fast on startup and cannot be intervened. But we can change the configuration of this so called `GRUB` screen

```bash
#change config file
/etc/default/grub

# GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=5

# run grub update to activate changes
sudo update-grub
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Kernel

The kernel is the core part of any operating system. It takes care of:

- Scheduling and resource allocation
- Inter-process communication (IPC)
- Physical and virtual memory
- Handles allocation and deallocation
- supports various file systems (e.g., ext4, XFS, Btrfs)
- Handles the read and write to those
- Implements various network protocols (e.g., TCP/IP, Ethernet)
- Routing, packet filtering, and traffic control
- Enables applications to communicate with various devices

### Kernel Modules

A kernel module is a piece of code that can be loaded into the kernel on demand and at runtime. It allows us to extend the functionality of the kernel. This is how proprietary drivers are loaded, for example device drivers.

```bash
# shows currently loaded kernel modules
sudo lsmod
```

## `systemd`

`systemd` is the main process that manages the operating system. After the bootloader has given control to the kernel, the kernel will initialize and start the main process (`PID`=`1` => `systemd`). This process will then initiate all the rest (starting the system, mounting drives, starting services, configuring network connections, etc.)
`systemd` consists of many tools, such as time synchronization

**Unit Files:** These are the foundational elements of `systemd`, representing various units like services, targets, and timers. They define the dependencies and configurations essential for services to function properly.

**Service Units:** This specific type of unit represents active services running on the system. You can enable, disable, start, and stop these services using `systemd` commands. Examples include web servers and background processes. Only service units have file extension `.service`. A service can spawn processes.

There are different directories where `systemd` searches for unit files:

- `/lib/systemd/system`: Contains default unit files from the distribution.
- `/run/systemd/system`: A runtime configuration folder that isn't persistent (files are lost after reboot).
- `/etc/systemd/system`: Where custom configurations and overrides by admins are stored (overwrites files in `/lib/systemd/system`)

```bash
# find systemd paths
systemd-analyze --system unit-paths

# combined output of all relevant configurations (primary and overrides) of a service
systemctl cat <service_name>.service

# list all available units
systemctl list-units

# show status of a service
systemctl status <service>

# change status of a service
systemctl {start, stop, restart, reload} <service>
```

## `cgroup`

Control groups are a feature of the Linux kernel that allow processes to be organized into hierarchical groups to manage resource allocation effectively.

- A cgroup can span multiple processes - and even contain other cgroups
- If we start a sub-process, it will automatically be in the same cgroup as the parent
- We can configure a cgroup to not exceed a specific memory limit (resource limiting)
- We can define how much % of CPU resources this group may occupy
- We can also measure how much resources certain systems consume
- ... or freeze a group of processes
- This can be useful when multiple services (in an application sense) / containers share the same server (resource sharing)

```bash
# list Cgroup of main process
systemctl status

# inspect cgroups
systemd-cgtop
systemd-cgtop --depth=5 # nesting level

```

### limiting a process' memory

1. Create a `systemd` slice (=`cgroup` that is managed by `systemd`)

```bash
#e.g in the user systemd config directory
mkdir .config/systemd/user
nano browser.slice

# write config for memory maximum
[Slice]
MemoryHigh=100M

```

2. Find out where the actual exectuable file of the process is run by starting it and then inspecting the process

```bash
which [process_name] # e.g. firefox
# output will be
# /usr/bin/firefox
# -> this is not always reliable because of potential symlinks and different package managers!

ps -ef | grep firefox
# turns out this is our binary:
# /snap/firefox/7664/usr/lib/firefox/firefox
```

3. Run the `systemd` config for the target process with our slice

```bash
systemd-run --user --slice=browser.slice snap/firefox/7664/usr/lib/firefox/firefox
```

4. Reload all user unit files if necessary (if config is not picked up)

```bash
systemctl --user daemon-reload
```

## Targets

With `.target` files units can be grouped into specific goals, e.g. booting into a state without graphical interface

```bash
# get current target
systemctl get-default # output: graphical.target

# switch target without rebooting (doesn't change the default)
systemctl isolate [target_name]

# list available targets
systemctl list-units --type target --all

# change default target
systemctl set-default [target_name]

# enabling a unit (will be loaded on boot, and now)
systemctl enable --now [service_name]

# disabling a unit (will no longer be loaded on boot)
systemctl disable [service_name]

# stopping a service right now
systemctl stop [service_name]
```

In the configuration file of a systemd unit the `WantedBy=` parameter tells the unit which target it's needed by.
Targets can reference other targets as dependencies, they will then be loaded after one another.
Every target has a folder in `systemd/system`, e.g. `multi-user.target.wants` which loads all services (via symlinks) associated with that target (`WantedBy`)

## The unit config file

### [Unit] parameters

general configuration for the unit (all unit files)

- **Description** brief explanation, helps users understand the purpose of the unit
- **Documentation** links to relevant documentation
- **Requires** ensures other units are activated before the current unit, if the required units fail to start, the current unit will not start either (dependencies), this only applies to a unit starting, it does not actually wait for it to finish!
- **Wants** similar to `Requires` but current unit will still start even if wanted unit fails. Useful for optional dependencies.
- **After** ensures that current unit starts after other specified units. This helps to define the order of unit activation, but it's not a dependency.
- **Before** ensures current unit starts before specified units (in terms of order of unit activation, can also be skipped, if necessary)
- **Conflicts** other units that cannot launch at the same time with the current unit

### [Service] parameters

specific to service units, this config specifies how the service is started, how it's stopped, etc.

- **Type** defines startup behaviour. e.g. `simple` (stay main process), `forking` (starts in background), `oneshot`(process should only run once)
- **ExecStart** command to start service, incl. arguments and options (not a bash command!)
- **ExecStop** command to stop service (optional)
- **Restart** defines when the service should be automatically restarted, e.g. `no`, `on-success`, `on-failure`, `on-abnormal`, `on-abort`, `always`
- **User** under which the service should be run
- **Environment** env variables for the service

### [Install] parameters

how the unit is installed (enabled/disabled)

- **WantedBy** specifies the targets that should include this unit as a dependency, e.g. `multi-user.target`, `graphical.target`. It enables the unit to be started automatically at boot when `systemctl enable` is used

## customizing unit files

1. we can copy the unit file from `/lib/systemd/system` to `/etc/systemd/system` (this will overwrite the original file)
2. re-load all config files again by running `systemctl daemon-reload`
3. However, any changes made in the `[Install]` section can only be applied by disabling and re-enabling the service, `systemctl disable / enable [service]`
4. after removing a `/etc/systemd/system` custom file, make sure to run the `daemon-reload` again!

### the built-in way

1. the easier way to customize the configuration is to use `systemctl edit apache2.service`
2. this creates internally a new folder in `/etc/systemd/system/apache2.service.d`
3. from this folder, override files will be loaded, that can change certain parts of the initial configuration
4. as well, make sure to `restart` any services to load the new custom configurations

## creating and managing unit files

1. to create a new unit file use this command:

```bash
systemctl edit --force --full [service_name]

# e.g. my-network-log.service

[Unit]
Description=Ping a server and log it
Requires=network-online.target # this is better than just network.target, because it actually waits until the network is online
After=network-online.target  # make sure the order is correct (first the target and then this service!)

[Service]
Type=oneshot
StandardOutput=append:/network-log/log.txt
ExecStart=date '+%%T' # the extra % acts as a placeholder in systemd, similar to escape characters -> will expand to +%T
ExecStart=ping -c 4 google.com

[Install]
WantedBy=multi-user.target
```

### timers with `systemd`

1. service to be modified should be disabled
2. create a timer for the file (e.g. for my-network-log.service)

```bash
systemctl edit --force --full my-network-log.timer # force will create the file

[Unit]
Description=Run the network logging service on boot

[Timer]
OnActiveSec=5min # 5min after timer has been enabled
Unit=my-network-log.service

[Install]
WantedBy=timers.target
```

3. list all active timers with `systemctl list-timers`

### calendar events

these refer to one or more points in time at which a service should run

1. use `systemd-analyze timestamp now` to find out how to use the placeholders in the calendar format

```bash
# Output:
Original form: now
Normalized form: Tue 2026-07-21 13:27:06 CEST # this is our default
       (in UTC): Tue 2026-07-21 11:27:06 UTC
   UNIX seconds: @1784633226.759902
       From now: 493us ago
```

2. use this to specify a timer's run times:

```bash
# .timer file:

[Timer]
OnCalendar=*-*-* *:0,15,30,45 # runs every 15 minutes, every day, every hour (*)
```

3. we can also validate this calendar timer

```bash
systemd-analyze calendar '*:0,15,30,45'

# Output:
  Original form: *:0,15,30,45
Normalized form: *-*-* *:00,15,30,45:00
    Next elapse: Tue 2026-07-21 13:45:00 CEST # shows next event time
       (in UTC): Tue 2026-07-21 11:45:00 UTC
       From now: 12min left # and how long to go

```

4. we can also use this command to find out the normalized form (for config files)

```bash
systemd-analyze calendar 'hourly'

# Output:
  Original form: hourly
Normalized form: *-*-* *:00:00
    Next elapse: Tue 2026-07-21 14:00:00 CEST
       (in UTC): Tue 2026-07-21 12:00:00 UTC
       From now: 25min left
```

5. let's edit our original timer

```bash
systemctl edit --full my-network-log.timer # --full will do a proper edit (not just override)

[Unit]
Description=Run the network logging service every 15 minutes

[Timer]
OnCalendar=*-*-* *:0,15,30,45

Unit=my-network-log.service

[Install]
WantedBy=timers.target
```

6. make sure to restart the serivce (and reload the config!)

## `journald` and `journalctl`

`journald` manages system logs and replaces traditional `syslog` ().
Syslog and journald differ significantly in their approaches to logging and storing log data. Here are the key differences:

| Aspect                | `syslog`                                                                                                           | `journald`                                                                                                                                                                  |
| --------------------- | ------------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Format of Logs        | Traditionally logs messages in plain text format, making them easily readable without requiring any special tools. | Uses a binary format for logs, which allows for more efficient storage and management of log data. Special commands, such as `journalctl`, are required to read these logs. |
| Centralized Logging   | Often requires multiple log files for different applications and services, leading to fragmentation.               | Provides a centralized logging solution where various services log messages into a single system, making it easier to manage and analyze logs.                              |
| Log Retention         | Generally relies on external tools for log rotation and retention management.                                      | Automatically manages log retention based on configured limits (like size), deleting older logs as needed without manual intervention.                                      |
| Boot Logging          | May not capture logs generated during the boot process if the logging service isn't started yet.                   | Can log messages that occur during the boot phase, as it collects data from a temporary buffer once it starts.                                                              |
| Querying Capabilities | Requires additional tools like `grep` to filter through log files, which can lead to errors in searching.          | Allows for structured querying of logs based on services and other criteria, improving the accuracy and efficiency of log analysis.                                         |

`journald` is designed as a more modern logging solution, addressing many limitations inherent in the traditional syslog framework. It can handle logs more effectively, particularly in complex environments where many services need to be monitored.

```bash
# display all logs
journalctl

# only show current boot
journalctl -b

# show all boot processes
journalctl --list-boots

# show logs of specific boot id
journalctl -b -34

# filter logs by unit
journalctl -u [unit_name]

# filter by time range
journalctl -u my-network-log --since '2026-06-01' --until '2026-07-21'

# reverse output
journalctl -r

# follows logs in real time
journalctl -f

# send a message into the journalctl log (with identifier)
echo 'message' | systemd-cat -t 'identifier_name'

# filter by identifier
journalctl -f -t 'identifier_name'
```
