reboot-if-needed-for-upgrade
=========

Reboots server and waits for it to come back online. By default reboots only if reboot operation is pending due to apt-get upgrade operation (/var/run/reboot-required)

Requirements
------------

Server should be accessible from ansible machine via direct TCP by it IP.

Role Variables
--------------

Role checks if reboot is pending, reboots server and waiting until it port (default 22, ssh) is available.

It will works only if you have direct access to that server. Normally inventory_hostname used to check if server is online. If you have trouble to get external (white) IP from instance, checkout get-external-ip-via-dyndns role.


- ip_to_probe: ip address to wait (default inventory_hostname)
- port_to_probe: port to probe (default 22, ssh)
- delay_before_probe: delay after reboot command (default 10)
- probe_timeout: max waiting time (if ip_to_probe:port_to_probe is on replying in probe_timeout time, role failing. default 70 seconds)
- force_reboot: Reboot regardless of /var/run/reboot-required (default: False)



Example Playbook
----------------

Simple:

    - hosts: servers
      vars:
        probe_timeout: 360
      roles:
         - { role: reboot-if-needed-for-upgrade }

Tricky:

    - hosts: servers
      vars:
        port_to_probe: 2222
      role:
        - get-external-ip-via-dyndns
        - reboot-if-needed-for-upgrade ip_to_probe="{{external_ip}}"


License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
