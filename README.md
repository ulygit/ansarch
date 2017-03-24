Ansarch
=============

Ansarch is a set of [Ansible](http://www.ansibleworks.com/)
playbooks for installing, configuring, and maintaining
[Arch Linux](https://www.archlinux.org/) hosts. It can also perform the
basic tasks necessary to safeguard a freshly installed server, so you can
go from nothing to a relatively secure system in no time flat.

Overview
--------

If you apply the entire _site.yml_ playbook to your hosts, the following
tasks will be handled for you by the "common" role:

* an updated pacman mirrorlist will be downloaded
* an non-root administrative user will be created
* OpenSSH will be configured more securely
* basic ingress firewall rules will be put into place
* kernel parameters will be set to harden the network stack
* the Network Time Protocol (NTP) service will be configured

_Note that you don't have to apply all of these tasks; everything is
[tagged](http://www.ansibleworks.com/docs/playbooks2.html#tags) for
flexibility._

Initial Setup
-------------

    $ git clone https://github.com/ulygit/ansarch.git
    $ cd ansarch/

### Prerequisites

* Ansible v1.3+
* Arch Linux host(s)
* root-level access on the host(s), directly or via sudo
* an [inventory file](http://www.ansibleworks.com/docs/patterns.html)

For the inventory hosts file, it's easiest to keep it in the same
directory as Ansarch so Ansible can find the correct *group_vars*.
By default, things are configured to look for a file named "hosts", but
you can override that either by setting the `ANSIBLE_HOSTS` environment
variable to point to its full path, or use the `-i <inventory file>` flag
on all of your Ansible commands.

### Bootstrapping

Arch Linux doesn’t have Python v2 installed by default, which is
a dependency for Ansible. Luckily we can use the
[raw module](http://ansibleworks.com/docs/modules.html#raw) to fix that:

> For the bootstrapping instructions, I'm assuming that you can connect to
> your hosts as the _root_ user...adjust these commands as necessary.

    $ ansible all -m raw -a '/usr/bin/pacman -Sy --noconfirm python2' --user=root

Always having to specify the user can get annoying, and using _root_
directly isn't the most secure practice. Instead we can create a new
administrative user based on your current username and
[ssh key](https://wiki.archlinux.org/index.php/SSH_keys) by running just
the "bootstrap" tagged tasks from the playbook:

    $ ansible-playbook site.yml --tags=bootstrap --user=root

Now you should be able to connect as yourself for all future tasks:

    $ ansible all -m ping

### Firewall Considerations

Only ssh (TCP port 22) is allowed through the firewall out of
the box, so you'll have to update the appropriate templates if you want to
expose other services.

Playbook Options
----------------

Most of the tasks performed by Ansarch can be customized by setting
the value of particular variables. The default values for these variables
are set and documented in the `group_vars/all` file on the controlling
machine.

If you just want to override the values for a single run, you can use the
`--extra-vars` flag:

    $ ansible-playbook site.yml --extra-vars="update_mirrorlist=true"

License
-------

Ansarch is provided under the terms of the
[ISC License](https://en.wikipedia.org/wiki/ISC_license).

Copyright &copy; 2013&#8211;2014, [Aaron Bull Schaefer](mailto:aaron@elasticdog.com). 
Copyright &copy; 2017 [Ulises M](mailto:ansarch@bfjournal.com).
