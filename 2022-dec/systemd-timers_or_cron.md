
# Systemd Timers or Cron?

August 11, 2022 by RB

Timers are systemd unit files whose name ends in .timer that control .service files or events. Timers can be use as an alternative to cron. Timers have built-in support for calendar time events, and monotonic time events, and can be run asynchronously.

# Benefits:

systemd units can do everything:

* have dependencies (other units)

* fine granularity for triggering (hw state changes, other events systemd lets you react to)

* easy management via systemctl enable/disable/start/stop

* Timer events can be scheduled based on finish times of executions some delays can be set between executions.

* OnFailure

* Environment File and other systemd unit goodies

* systemcall filters

* user/group ids

* membershipcontrols

* nice value

* OOM score

* IO scheduling class and priority

* CPU scheduling policy CPU

* affinity umask

* timer slacks

* secure bits

* network access

* Basic structure

# Cron job line consists of:

1. scheduling information

1. action information.

Systemd timers work the same way but split each concept into its own file with two unit files: a `service` and a `timer`. One benefit you'll notice right away is that this lets us start/test these service units independently of their timers.

## Create demo service
```
$ sudo vim /usr/local/bin/backup.sh
```
```
#!/usr/bin/env bash
echo "I just ran a $(date)"
exit 0
```

## Make it executable:
```
$ sudo chmod +x /usr/local/bin/backup.sh
```
## Create systemd service unit file
```
$ sudo vim /lib/systemd/system/backup.service
```
```
[Unit]
Description=Run the databackup echo service

[Service]
ExecStart=/usr/local/bin/backup.sh
```

## Create systemd timer unit file
```
$ sudo vim /lib/systemd/system/backup.timer
```
or
```
$ sudo systemctl cat backup.timer
```
```
[Unit]
Description=backup service timer

[Timer]
OnBootSec=0min
OnCalendar=*:*:0/10
Unit=backup.service

[Install]
WantedBy=multi-user.target
```

## Cool features: testing directives

Test your time formats without saving/re-running your cronjob a bunch of times.
```
$ systemd-analyze calendar "*:*:0/10"
```

## Based on: 
```
$ man systemd.time
```

## Enabling and Starting it:

Tell systemd about the new unit files, enable the timer, and you're good to go!
```
$ sudo systemctl daemon-reload

$ sudo systemctl start backup.timer
```
## If you want to keep this around after a reboot:
```
$ sudo systemctl enable backup.timer
```

## Logging

Just like any other systemd unit -- check the journal!
```
$ journalctl -fu backup.service
```