# Enterprise Linux Lab Report

- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

The following report will explain the installation and configuration of `SAMBA` and `FTP`, using an `VSFTPD` `Ansible Role`, for usage as a file server.
## Test plan

* Start the machine pr011 from a terminal with following command: `vagrant up pr011`
* Connect with the virtuele machine through SSH: `vagrant ssh pr011`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`


## Procedure/Documentation

**Add server `pr011` as a `Vagrant` host:**

By adding `pr011` to `vagrant-hosts.yml`, `Vagrant` knows that it needs to create the virtual machine. The variables underneath also set the IP for the virtual machine. 

    - name: pr011
      ip: 172.16.0.11
      netmask: 255.255.0.0

**Configure `pr011` as a `Vagrant` host**

This is our `Ansible Playbook`, in this file we will give the variables for every specific server. To configure the necessary services, we will use existing `Ansible` roles.

***Add the necessary `Ansible` roles to the `Ansible Playbook`***

    - hosts: pr011
      sudo: true
      roles:
        - bertvv.rh-base
        - bertvv.samba
        - bertvv.vsftpd

**Configure the required shares for `pr011`**

    post_tasks:
      - name: ACL share sales
        acl:
          path: /srv/shares/sales
          entity: management
          etype: group
          permissions: rx
          state: present
      - name: ACL share it
        acl:
          path: /srv/shares/it
          entity: management
          etype: group
          permissions: rx
          state: present

**Configure the required services for `pr011`**

Make the file `pr011` in the folder `ansible/host-vars/`. The variables in this file are only applied to the server `pr011` if `Ansible` is ran against it. It is used to configure the `Ansible Roles` that were defined earlier in the `Ansible Playbook`.

***Add the variables for the addition of the DNS of the router***

      dns_intname: "eth1"
      dns_nameservers:
        - DNS1=172.16.255.254
      dns_search: avalon.lan

***Add the variables for the addition of SAMBA,DNS and the SAMBA-Client to the FireWall***

      rhbase_firewall_allow_services:
        - samba
        - ftp
        - samba-client

***Add the variables for the creation of the `User Groups`***

      rhbase_user_groups:
        - management
        - technical
        - sales
        - it
        - public

***Add the variables for the creation of the users describes in `avalon-employees.csv`***

The passwords assigned to the users are the same as their respective username. These passwords have either been encrypted using [MKPasswd](https://www.mkpasswd.net/index.php) or by using the `Ansible Vault`. The designated steps to encrypt passwords with `Ansible Vault` are discussed in `report-05`.

      rhbase_users:
        - name: stevenh
          comment: 'Steven Hermans'
          groups:
            - management
            - public
          password: '$1$Y8Sr3EXF$g.y6lPfmbmCCkKLgD5Nko0'
          shell: /sbin/nologin
        - name: stevenv
          comment: 'Steven Van Daele'
          groups:
            - technical
            - public
          password: '$1$ZEwAGKzo$FGzLrYBaPmogF8v7a3Qwt0'
          shell: /sbin/nologin
        - name: leend
          comment: 'Leen De Meester'
          groups:
            - technical
            - public
          password: '$1$X3syz3XK$zjk8oRq7/l9Fc4EvuGbmy0'
          shell: /sbin/nologin
        - name: svena
          comment: 'Sven Aerens'
          groups:
            - sales
            - public
          password: '$1$7ld2IY7o$xpBkaJr5u/3DPvJ7uYtsA1'
          shell: /sbin/nologin
        - name: nehirb
          comment: 'Nehir Başar'
          groups:
            - it
            - public
          password: '$1$DLGmwV7v$ZLZcoYO0KVseUNlX2pnxK/'
        - name: alexanderd
          comment: 'Alexander De Coninck'
          groups:
            - technical
            - public
          password: '$1$Y.aXDYgm$b/NUYGTachMV.bM1C7xB.1'
          shell: /sbin/nologin
        - name: krisv
          comment: 'Kris Vanhaverbeke'
          groups:
            - management
            - public
          password: '$1$onejy5Tc$dQ1X3JCGmHOkYEw7s/aYZ/'
          shell: /sbin/nologin
        - name: benoitp
          comment: 'Benoit Pluquet'
          groups:
            - sales
            - public
          password: '$1$ESiuPYN1$CA0EdinVNyuGUidKLA.fI.'
          shell: /sbin/nologin
        - name: anc
          comment: 'An Coppens'
          groups:
            - technical
            - public
          password: '$1$LiW9jsaL$IQMjnVeL8HBk44V.k.Lv0/'
          shell: /sbin/nologin
        - name: elenaa
          comment: 'Elena Andreev'
          groups:
            - management
            - public
          password: '$1$XF/gaVNq$DbC1/yFokeNMMXInLsMo50'
          shell: /sbin/nologin
        - name: evyt
          comment: 'Evy Tilleman'
          groups:
            - technical
            - public
          password: '$1$kPBaRrf3$gJDP4ucKp8rVl2Boz3W44/'
          shell: /sbin/nologin
        - name: christophev
          comment: 'Christophe Van der Meershce'
          groups:
            - it
            - public
          password: '$1$v93zyvr/$CdHLF47/HOJZZzHE0DlrX/'
        - name: stefaanv
          comment: 'Stefaan Vernimmen'
          groups:
            - technical
            - public
          password: '$1$TS4nuqQZ$zrhsvsZLa7DITbdFvKF9h/'
          shell: /sbin/nologin
        - name: jens
          comment: 'Jens Du Four'
          groups:
            - wheel
            - it
            - public
          password: '$1$w/VkEZta$LSbA5yef.igFmSgeRTMnq0'

***Configure the `NETBIOS` and revoke acces for guest users***

      samba_netbios_name: 'files'
      samba_map_to_guest: 'Never'

***Add the following variables for the creation of the `SAMBA`-users***

      samba_users:
        - name: stevenh
          password: stevenh
        - name: stevenv
          password: stevenv
        - name: leend
          password: leend
        - name: svena
          password: svena
        - name: nehirb
          password: nehirb
        - name: alexanderd
          password: alexanderd
        - name: krisv
          password: krisv
        - name: benoitp
          password: benoitp
        - name: anc
          password: anc
        - name: elenaa
          password: elenaa
        - name: evyt
          password: evyt
        - name: christophev
          password: christophev
        - name: stefaanv
          password: stefaanv
        - name: jens
          password: jens

***Add the following variables for the creation of the `SAMBA`-shares***

      samba_shares:
        - name: public
          comment: 'A public share'
          group: public
          browsable: 'yes'
          writeable: 'yes'
          write_list: +public
          valid_users: +public
          create_mode: 775
          directory_mode: 775
          path: /srv/shares/public
        - name: management
          comment: 'Management share: inaccesible for all outside employees'
          group: management
          browsable: 'yes'
          writeable: 'yes'
          write_list: +management
          valid_users: +management
          create_mode: 770
          directory_mode: 770
          path: /srv/shares/management
        - name: technical
          comment: 'Techincal share: visible for employees outside their unit, but not writable'
          group: technical
          browsable: 'yes'
          writeable: 'yes'
          write_list: +technical
          valid_users: +technical, +public
          create_mode: 775
          directory_mode: 775
          path: /srv/shares/technical
        - name: sales
          comment: 'Sales share: visible to management but not writable'
          group: sales
          browsable: 'yes'
          writeable: 'yes'
          write_list: +sales
          valid_users: +sales, +management
          create_mode: 770
          directory_mode: 770
          path: /srv/shares/sales
        - name: it
          comment: 'IT share: visible to management but not writable'
          group: it
          browsable: 'yes'
          writeable: 'yes'
          write_list: +it
          valid_users: +it, +management
          create_mode: 770
          directory_mode: 770
          path: /srv/shares/it

***Add the following variables to revoke anonymous access to the FTP Server. Only registerd users are allowed acces to the file server***

      vsftpd_anonymous_enable: false
      vsftpd_local_enable: true
      vsftpd_local_root: /srv/shares

## Test report

After following the test plan the following output should be returned:

    Running test /vagrant/test/common.bats
     ✓ EPEL repository should be available
     ✓ Bash-completion should have been installed
     ✓ bind-utils should have been installed
     ✓ Git should have been installed
     ✓ Nano should have been installed
     ✓ Tree should have been installed
     ✓ Vim-enhanced should have been installed
     ✓ Wget should have been installed
     ✓ Admin user jens should exist
     ✓ Custom /etc/motd should have been installed

    10 tests, 0 failures
    Running test /vagrant/test/pr011/samba.bats
     ✓ The ’nmblookup’ command should be installed
     ✓ The ’smbclient’ command should be installed
     ✓ The Samba service should be running
     ✓ The Samba service should be enabled at boot
     ✓ The WinBind service should be running
     ✓ The WinBind service should be enabled at boot
     ✓ The SELinux status should be ‘enforcing’
     ✓ Samba traffic should pass through the firewall
     ✓ Check existence of users
     ✓ Checks shell access of users
     ✓ Samba configuration should be syntactically correct
     ✓ NetBIOS name resolution should work
     ✓ read access for share ‘public’
     ✓ write access for share ‘public’
     ✓ read access for share ‘management’
     ✓ write access for share ‘management’
     ✓ read access for share ‘technical’
     ✓ write access for share ‘technical’
     ✓ read access for share ‘sales’
     ✓ write access for share ‘sales’
     ✓ read access for share ‘it’
     ✓ write access for share ‘it’

    22 tests, 0 failures
    Running test /vagrant/test/pr011/vsftp.bats
    ✓ VSFTPD service should be running
    ✓ VSFTPD service should be enabled at boot
    ✓ The ’curl’ command should be installed
    ✓ The SELinux status should be ‘enforcing’
    ✓ FTP traffic should pass through the firewall
    ✓ VSFTPD configuration should be syntactically correct
    ✓ Anonymous user should not be able to see shares
    ✓ read access for share ‘public’
    ✓ write access for share ‘public’
    ✓ read access for share ‘management’
    ✓ write access for share ‘management’
    ✓ read access for share ‘technical’
    ✓ write access for share ‘technical’
    ✓ read access for share ‘sales’
    ✓ write access for share ‘sales’
    ✓ read access for share ‘it’
    ✓ write access for share ‘it’

## Resources

[Wat is SAMBA](https://www.samba.org/samba/what_is_samba.html)

[vsftpd](https://galaxy.ansible.com/bertvv/vsftpd/)

[ACL Module](http://docs.ansible.com/ansible/latest/acl_module.html)

[SAMBA Playbook Example](https://github.com/bertvv/ansible-role-samba/blob/docker-tests/test.yml)

[vsftpd Playbook Example](https://github.com/bertvv/ansible-role-vsftpd/blob/vagrant-tests/test.yml)

[SELinux vsftpd](https://security.stackexchange.com/questions/62357/selinux-security-context-public-content-t-for-vsftpd)
