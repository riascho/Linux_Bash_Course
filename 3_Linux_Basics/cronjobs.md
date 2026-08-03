# Cronjobs

Cronjobs allow us to repeatedly execute programs on our computer, e.g. execute a backup script every day.

We could also use `systemd` timer units with `OnCalendar`, but cronjobs are more portable (can run on Unix (incl. Mac), not just Linux) and built-into many finished products (e.g. n8n, webhosts).

**Different cron implementations**

- **vixie-cron:** used in Ubuntu as the cron implementation (`cron` package)
- **anacron:** used in Ubuntu to support jobs that should run at regular intervals. if system is shut down, they will be run during the next boot. different configuration than normal `cron`. `anacron` can detect system running on battery or net power.
- **cronie:** cron implementation on CentOS

## cron daemon `crond`

- the cron daemon is a background process that manages scheduled tasks
- executes commands at pre-specified intervals
- will wake up every minute and check is something needs to be executed
- named after Greek word "chronos" (time)
- it reads crontab files:
  - user-specific: `/var/spool/cron/crontabs` or `/var/cron/tabs`
  - system-wide: `/etc/crontab` (must be owned by root and not writable by others)
  - on debian/ubuntu: `/etc/cron.d` (for 3rd party software)

## Creating cronjobs (user-specific)

```bash
# edit crontab file
crontab -e

# specify editor to open with
EDITOR=nano crontab -e

# list all cronjobs
crontab -l
```

- cronjob files will be stored user-specific (depending who's using the terminal)

### Format

1. environment variables, e.g. `SHELL` (default is `/bin/sh`) or `PATH` (default is `/usr/bin:/bin`)
2. actual cronjob: `[Minute] [Hour] [Day] [Month] [Day-of-Week] [Command]

```bash
SHELL=/bin/bash # where command is executed
PATH=/usr/local/bin:/usr/local/sbin:/sbin:/usr/sbin:/bin:/usr/bin # where executables are
MAILTO="address@domain.com" # per default, if no output redirect is specified, the email address of the cronjob's user will be used
# needs mail transfer agent (MTA) installed!
5 3 * * * ping -c 10 google.com # pings google 10 times every day at 3:05am and emails user
* * * * * ping -c 10 google.com >> ~/ping-cron.txt # runs every minute! and appends output  to file
```

**Allowed values**

| Field       | Range                                     |
| ----------- | ----------------------------------------- |
| Minute      | `0-59`                                    |
| Hour        | `0-23` (24h clock)                        |
| Day         | `1-31` (day of month)                     |
| Month       | `1-12` or `jan`-`dec`                     |
| Day-of-Week | `0-7` or `sun`-`sat` (0 and 7 are Sunday) |

**Operators**

- `*` — any value ("every")
- `,` — list of values, e.g. `1,15`
- `-` — range of values, e.g. `1-5`
- `/` — step within a range, e.g. `*/10` (every 10th) or `0-30/5` (every 5th between 0 and 30)

### Common time specifications

| Specification  | Meaning                                 | Example                                           |
| -------------- | --------------------------------------- | ------------------------------------------------- |
| `* * * * *`    | every minute                            | `* * * * * /usr/local/bin/heartbeat.sh`           |
| `*/5 * * * *`  | every 5 minutes                         | `*/5 * * * * /usr/local/bin/check-disk.sh`        |
| `0 * * * *`    | every hour (at minute 0)                | `0 * * * * /usr/local/bin/rotate-logs.sh`         |
| `30 * * * *`   | every hour at half past                 | `30 * * * * /usr/local/bin/sync.sh`               |
| `0 */2 * * *`  | every 2nd hour (0:00, 2:00, 4:00, …)    | `0 */2 * * * /usr/local/bin/fetch-mail.sh`        |
| `0 9-17 * * *` | every hour between 9:00 and 17:00       | `0 9-17 * * * /usr/local/bin/report.sh`           |
| `0 3 * * *`    | every day at 3:00am                     | `0 3 * * * /usr/local/bin/backup.sh`              |
| `15 2 * * 1`   | every Monday at 2:15am                  | `15 2 * * 1 /usr/local/bin/weekly-backup.sh`      |
| `0 6 * * 1-5`  | every weekday (Mon–Fri) at 6:00am       | `0 6 * * 1-5 /usr/local/bin/standup-reminder.sh`  |
| `0 20 * * 6,0` | every Saturday and Sunday at 8:00pm     | `0 20 * * 6,0 /usr/local/bin/weekend-job.sh`      |
| `0 0 1 * *`    | first day of every month at midnight    | `0 0 1 * * /usr/local/bin/monthly-invoice.sh`     |
| `0 0 1 */3 *`  | every 3rd month (quarterly) on the 1st  | `0 0 1 */3 * /usr/local/bin/quarterly-cleanup.sh` |
| `0 0 1 1 *`    | once a year, 1st of January at midnight | `0 0 1 1 * /usr/local/bin/yearly-archive.sh`      |

- careful: if **both** Day and Day-of-Week are set (not `*`), cron runs the job when **either** matches, e.g. `0 0 13 * 5` runs on the 13th of the month _and_ on every Friday

### Special strings

instead of the five fields, some shortcuts can be used:

| String                  | Equivalent to | Meaning                          |
| ----------------------- | ------------- | -------------------------------- |
| `@yearly` / `@annually` | `0 0 1 1 *`   | once a year at midnight, Jan 1st |
| `@monthly`              | `0 0 1 * *`   | once a month at midnight, 1st    |
| `@weekly`               | `0 0 * * 0`   | once a week, Sunday at midnight  |
| `@daily` / `@midnight`  | `0 0 * * *`   | once a day at midnight           |
| `@hourly`               | `0 * * * *`   | once an hour at minute 0         |
| `@reboot`               | —             | once, at system startup          |

```bash
@daily /usr/local/bin/backup.sh
@reboot /usr/local/bin/start-tunnel.sh
```

> tip: [crontab.guru](https://crontab.guru) explains a cron expression in plain English

## Debugging

```bash
# use journal to check cron specific log files in case of errors
journalctl -u cron.service -f # only last few lines
```

## Creating cronjobs (system-wide)

- need to edit the `/etc/crontab` file directly (no `crontab` needed!)
- cronjobs can be run as any user

### Format

The format is slightly different

```bash
[Minute] [Hour] [Day] [Month] [Day-of-Week] [User] [Command]
# command will be executed by specified user
```

## Best Practices

- be aware of different cron implementations (`cron` vs. `anacron`)

### Planning & Scheduling

- distribute tasks evenly, avoid peak hours
- avoid executing the same taks multiple times (for small intervals consider using `flock`)

### Logging & Error handling

- log errors and analyze them regularly
- implement error checks within our scripts

### Security

- run tasks with the least privileges possible (e.g. files created through cronjob user)
- keep scripts and commands secure with proper permissions
- avoid storing sensitive information in crontab files

### Testing

- test scripts thoroughly before adding to crontab
- make sure they work with the `PATH` variable that cron provides (or set your own, or use ultimate paths)
- monitor initial runs to ensure proper execution
