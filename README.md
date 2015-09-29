RHEL 5 DISA STIG
================

Configure RHEL 5 machine to be DISA STIG compliant. CAT I findings will be corrected by default. CAT II and CAT III findings can be corrected by setting the appropriate variable to enable those playbooks.

This role **will make changes to the system** that could break things. This is not an auditing tool but rather a remediation tool to be used after an audit has been conducted.

The role tries to be helpful and pause to let you know it found something. You can disable this behaviour if you want to run it unattended by setting `rhel5stig_fullauto` to `True`.

## IMPORTANT INSTALL STEP

If you want to install this via the `ansible-galaxy` command you'll need to run it like this: 

`ansible-galaxy install -p roles nousdefions.STIG-RHEL5,devel`

Based on [Red Hat Enterprise Linux 5 STIG Version 1 Release 11 - 2015-07-24](http://iase.disa.mil/stigs/os/unix-linux/Pages/index.aspx).


Requirements
------------

You should carefully read through the tasks to make sure these changes will not break your systems before running this playbook.

Role Variables
--------------
There are many role variables defined in `defaults/main.yml`. This list shows the most important.

| Name                              | Default Value         | Description          |
|-----------------------------------|-----------------------|----------------------|
| `rhel5stig_cat1`                  | `True`                | Correct CAT I findings |
| `rhel5stig_cat2`                  | `False`               | Correct CAT II findings |
| `rhel5stig_cat3`                  | `False`               | Correct CAT III findings |
| `rhel5stig_fullauto`              | `True`                | Run the role without pausing |
| `rhel5stig_use_dhcp`              | `True`                | Whether the system should use DHCP or Static IPs. **Setting this to False is dangerous.** |
| `rhel5stig_system_is_router`      | `False`               | Whether on not the target system is acting as a router. Disables settings that would break the system if it is a acting as a router. |
| `rhel5stig_root_email_address`    | `foo@baz.com`         | Address where system email is sent |
| `rhel5stig_xwindows_required`     | `False`               | Whether or not X Windows is is use on taregt systems. Disables some changes if X Windows is not in use. |
| `rhel5stig_ipv6_in_use`           | `False`               | Whether or not ipv6 is in use of the target system. This is set automatically to `True` if ipv6 is found to be in use. |
| `rhel5stig_tftp_required`         | `False`               | Whether or not TFTP is required. This will prevent the removal of `tftp` and `tftp-server` packages. It will also  reconfigure the `tftp-server` to run securely. |
| `rhel5stig_change_grub_password`  | `False`               | Whether or not to update the Grub password even if a hash already exists in `/boot/grub/grub.conf`. |
| `rhel5stig_bootloader_password`   | `[random data]`       | The new Grub password to use if `rhel6stig_change_grub_password` is `True` |

Dependencies
------------

Ansible > 1.8

Example Playbook
-------------------------

Make sure to include the vars_prompt section in your playbook. It is needed for the tasks that set the grub password.

    - hosts: servers
      sudo: yes

      roles:
         - { role: RHEL5-STIG, rhel5stig_cat1: True, rhel5stig_cat2: True, rhel5stig_cat3: False }


Tags
----
Many tags are available for precise control of what is and is not changed. When running this playbook with tags, always include the `prelim_tasks` tag. This will run all the setup tasks that gather information and set variables used by subsequest tasks. If run without `prelim_tasks`, certain tasks may fail.

Some examples of using tags:

    # Only remediate ssh
    ansible-playbook site.yml --tags="prelim_tasks,ssh"

    # Don't change SNMP or postfix
    ansible-playbook site.yml --skip-tags="postfix,mail,snmp"


License
-------

MIT
