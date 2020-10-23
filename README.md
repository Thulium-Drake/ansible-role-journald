# Centralized logging via Journald by Ansible
This role provides a means to collect system logs on a central system via systemd-journald.

All logs are saved on the system designated as journald_server in the vars. When the role
is run on that host, it will configure the journal collector.

# Usage
* Install the role (either from Galaxy or directly from GitHub)
* Copy the defaults file to your inventory (or wherever you store them) and
  fill in the blanks
* Add the role to your master playbook
* Run Ansible
* ???
* Profit!

## Analyzing logs
Searching or reading the logs collected can be done using the same journalctl tool you use for reading
local journals. Below are some examples:

* Tail all the journals coming in
```
journalctl -f -D /log
```

* Tail all journals from a specific unit file (on any machine)
```
journalctl -f -D /log -u my-thing.service
```

## SSL
When running this role with SSL enabled, make sure you have the following provided:

* CA certificate (selfsigned or otherwise) accessible for systemd-journal-upload user
* Server certificate for loghost system signed by that CA, accessible for systemd-journal-remote user

A self-signed 'solo' certificate _will not_ work!

## BUG: CentOS7 (or other systems with systemd 219)
The version of systemd-journal-remote in this version of systemd contains a bug where journals are
saved in a file named after the _receiving_ end of the transaction. In the case of the passive sources
this role configures, this means all journals are collected in a single file.
