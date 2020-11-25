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

Note that if you wish to run this role on systems with a version of Systemd higher then 234, _must_
arrange a client certificate for clients to authenticate against the journald server. This certificate
can be shared among all clients.

A sample script to install (provide the cert yourself) the certificate:

```
- name: 'Ensure journald client certificate group'
  group:
    name: 'journald-cert'
    system: true
    state: 'present'

- name: 'Ensure journald client certificate directory'
  file:
    path: '/etc/ssl/journald'
    state: 'directory'
    owner: 'root'
    group: 'journald-cert'
    mode: 0770

- name: 'Ensure certificate files'
  copy:
    src: "{{ cert.src }}"
    dest: "{{ cert.dest }}"
    owner: 'root'
    group: 'journald-cert'
    mode: 0440
  loop:
    - src: "{{ journald_certificate_source }}"
      dest: '/etc/ssl/journald/client.crt'
    - src: "{{ journald_key_source }}"
      dest: '/etc/ssl/journald/client.key'
  loop_control:
    loop_var: 'cert'
```

This approach has been tested on CentOS7 and CentOS8 in the same environment.

## BUG: CentOS7 (or other systems with systemd 219)
The version of systemd-journal-remote in this version of systemd contains a bug where journals are
saved in a file named after the _receiving_ end of the transaction. In the case of the passive sources
this role configures, this means all journals are collected in a single file.
