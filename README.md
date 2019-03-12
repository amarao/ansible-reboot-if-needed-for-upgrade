reboot-if-needed-for-upgrade
=========

This playbook reboots server and waits for it to come back online.
By default reboots only if reboot operation is pending due to apt-get upgrade operation (/var/run/reboot-required)

Requirements
------------

Server should be accessible from ansible machine via direct TCP by it IP.

Changelog
---------
- add pre-flight check if port is reachable. Use `skip_pre_reboot_check=true` to skip it.
- reworked it to work around nasty SSH errors: `Failed to connect to the host via ssh: Shared connection to 192.168.0.2 closed.`, now it uses `ansible_default_ipv4.address`.


Role Variables
--------------

Role checks if probing port is reachable, reboot server is reboot is requested (by apt or by anyone setting a reboot flag), waits until port become available again.

It will works only if you have direct access to that server. Normally `ansible_default_ipv4.address` is used to check if server is online. If you have trouble to get external (white) IP from instance, checkout get-external-ip-via-dyndns role.


Variables:
- `ip_to_probe`: ip address to wait (default to `ansible_default_ipv4.address`, which is gathered by setup module). It must be a proper IP or domain name, and you can't use names from your `ssh_config` here.
- `port_to_probe`: port to probe (default 22, ssh)
- `delay_before_probe`: delay after reboot command (default 10)
- `probe_timeout`: max waiting time (if `ip_to_probe`:`port_to_probe` is not replying in `probe_timeout` time, role fails. default 70 seconds)
- `force_reboot`: Reboot regardless of /var/run/reboot-required (default: False)
- `reboot_delay`: Delay before issuing reboot command (default: 2 seconds), to help ssh to process command
- `reboot_flag`: Path to reboot flag
- `skip_pre_reboot_check`: Skip pre-preboot port check. Normally playbook will stops if it can not reach port before doing any reboot
- `pre_reboot_timeout`: How long to wait a port at pre-reboot check

Example Playbooks
-----------------

Simple:
```
- hosts: servers
  vars:
    probe_timeout: 360
  roles:
     - amarao.reboot_if_needed_for_upgrade
```
Tricky:
```
- hosts: servers
  vars:
    port_to_probe: 2222
    ip_to_probe: "{{ ansible_ssh_host }}"
    delay_before_probe: 120
    probe_timeout: 360
    force_reboot: True
    skip_pre_reboot_check: true
  role:
    - amarao.reboot_if_needed_for_upgrade
```

License
-------

BSD

Author Information
------------------
(C) 2016-2019, servers.com, by George Shuklin
