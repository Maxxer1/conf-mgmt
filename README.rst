conf-mgmt-like-a-sir
====================

Set of Ansible tasks to configure Gentoo-based workstations.

Intentionally none of the roles will install packages, and fail, if the binaries are not out there.

Usage
-----
To apply *defaults* one can run ``localhost.yml`` script.
::

    virtualenvvenv
    . venv/bin/activate
    pip install -r requirements.txt
    ./update && ./apply

Secrets
=======

Secrets handling via separated repository is supported by the ``apply``` script. Check it's header comments for more information.

Roles
=====

connman
-------

- Disables DNS proxy. I prefer to use dnsmasq.
- Disables NTP client. I prefer to use chrony.
- Disables hostname changes via DHCP.
- Disables timezone updates. It uses DBus, nothing listen here for the timezone changes. ¯\\_(ツ)_/¯
- Allow wheel group to use ``connmanctl`` and in general talk with connman via DBus.
- ensure ``/var/run/connman`` exists, opentmpfiles creates it, but unless triggered (like, reboot) it won't.

dnsmasq
-------

- Get ``dnsmasq.conf`` that either uses connman's ``/var/run/connman/resolv.conf``, directly Google DNS servers or OpenDNS, depending on ``upstream`` set to either ``connman``, ``google``, ``opendns`` or ``cloudflare``.

resolve_dns_via_localhost
-------------------------

- Set ``/etc/resolv.conf`` to ``nameserver 127.0.0.1`` and apply immutable bit on the file.

sshd
----

- Allow root login with key-only auth.
- Disable password login all together, when ``no_password_login`` set to ``True``.
- Change default port if ``sshd_port`` is set.

system_groups
-------------

- ``view_proc`` gid ``50001``
- ``with_symlinksifownermatch`` gid ``50002``
- ``with_audit`` gid ``50003``
- ``without_tpe`` gid ``50004``
- ``deny_client_socket`` gid ``50006``
- ``deny_server_socket`` gid ``50006``

system_user
-----------

Create user with name as ``user`` and group as ``group``. Optionally can get ``additional_groups`` parameter with colon separated additional groups.

hostname
--------

Set hostname to ``hostname`` variable.

timezone
--------

Set timezone to ``timezone`` variable.

locale
------

Set English UTF-8 locales with ISO-ish date format and Coreutils's long-iso time style.

unscd
-----

Enable unscd as dns cache.

fstrim
------

Send TRIM to underlying storage on all supported filesystems for unused blocks.

iwlwifi
-------

Control iwlwifi kernel module options

clean_tmp_dirs
--------------

Install script that is executed every hour by cron that clean /tmp, /var/tmp as well as users' temporary directories. Users can exclude themselves by creating ``.skip-cleaning`` file in ``$HOME/tmp``. Files and directories with mtime >= 24h will be removed from the temporary directories. Some excludes are added in script to not break Xorg etc.

Requires:

- ``bash`` in version 4
- ``chpst`` from ``busybox``
- ``tmpreaper``

disable_wireless_power_saving
-----------------------------

Disable 'power saving' for Wireless network interfaces. Useful for hosts that have WAN as wireless, to prevent inbound SSH connections from being lagish.

home_files
------------

Takes users from ``users`` list and deploy the configuration files from ``deploy`` list for them.

pam_limits
----------

Set pam_limits's configuration, max per-user processes to ``4096`` and max per-user file descriptors to ``4096``.

in_home_tmpdir
--------------

Upon login, create ~/tmp and set $TMP and $TMPDIR to point that directory.

default_umask_077
-----------------

Login shells gets umask of 077

dispatch-conf
-------------

Set dispatch-conf to use colordiff.

noclear_tty_on_boot
-------------------

Append --noclear to agetty so tty are not cleared at startup.

virtual_memory_tune
-------------------

Tune dirty (background) bytes, swappiness and vfs pressure.

portage
-------

Control /etc/portage content.

Variables:
    - local_use_flags - optional USE flags, host specific, additional to common one.


prefer_ipv4_over_ipv6
---------------------

Make glibc's resolver prefer A entries over AAAA.

acpid
-----

Deploy acpid hooks, to support hotkeys like sleep, volume, brightness and so on.

sudoers
-------

Deploy /etc/sudoers.d/* files


moar-network-setup
------------------

Script to configure firewall, vm bridges and poor's man switch via single local.d script, rather than via /etc/conf.d/ hackery.

Variables:

    - vm_bridges: int, numbers of bridges, will be used as sufix for interface name and as 3rd octet of 24bit subnet.
    - vm_bridges_network_prefix: prefix, for example `192.168` to get `192.168.<vm_bridge[0].0/24`
    - switch_bridge_interface: name of the 'switch' bridge interface, for example `br-eth-ports`
    - switch_bridge_member_interfaces: interfaces to add into 'switch'. for example `eth0 eth1 eth2`
    - switch_bridge_subnet: subnet on bridge, for example `172.17.17.0/24`
    - switch_bridge_ip: ip to set on 'switch' bridge, for example `172.17.17.1/24`
    - inbound_open_tcp_ports: TCP ports to open.
    - inbound_open_udp_ports: UDP ports to open.
    - inbound_respond_to_ping: control ping responses, enabled by `true` string (not bool!)
    - outbound_reject_from_users: list of user whom should not be able to make outgoing connections.

inotify_tune
------------

Allow user to have 256k inotify watchers.
