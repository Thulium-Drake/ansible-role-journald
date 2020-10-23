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
