# Enterprise Linux Lab Report

- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

The following report will explain the installation and configuration of LAMP (Linux-Apache-MariaDB-PHP) with Wordpress and self-signed SSL-certifcates.

![LAMP](https://lwstatic-a.akamaihd.net/kb/wp-content/uploads/2018/02/kb-lamp-stack.jpg)


## Test plan

* Start the machine pu004 from a terminal with following command: `vagrant up pu004`
* Connect with the virtuele machine through SSH: `vagrant ssh pu004`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`

## Procedure/Documentation

**Add server `pu004` as a `Vagrant` host**

By adding pu004 to `vagrant-hosts.yml`, `Vagrant` knows that it needs to create the virtual machine. The variables underneath also set the IP for the virtual machine. 

    - name: pu004
      ip: 192.0.2.50

**Configure pu004 in `ansible/site.yml`**

This is our `Ansible Playbook`, in this file we will give the variables for every specific server. To configure the necessary services, we will use existing `Ansible` roles.

***Add the necessary `Ansible` roles to the `Ansible Playbook`***

    - hosts: pu004
      become: true
      roles:
        - bertvv.rh-base
        - bertvv.httpd
        - bertvv.mariadb
        - bertvv.wordpress

***Add the following variables to configure `self-signed SSL-certifcates`***

    pre_tasks:
      - name: Copy web server private key
        copy:
          src: files/pu004.key
          dest: /etc/pki/tls/private/pu004.key
      - name: Copy web server certificate
        copy:
          src: files/pu004.crt
          dest: /etc/pki/tls/certs/pu004.crt

**Configure the required services for `pu004`**

Make the file `pu004.yml` in the folder `ansible/host-vars/`. The variables in this file are only applied to the server `pu004` if `Ansible` is ran against it. It is used to configure the `Ansible Roles` that were defined earlier in the `Ansible Playbook`.

***Add the variables for the addition of HTTP/HTTPS to the FireWall***

    rhbase_firewall_allow_services:
      - https
      - http

***Add the variables for the configuration of MariaDB***

    httpd_scripting: 'php'

    mariadb_databases:
      - name: wp_db

    mariadb_users:
      - name: wp_user
        password: CorkIgWac
        priv: '*.*:ALL,GRANT'

    mariadb_root_password: fogMeHud8

***Add the variables for the configuration of Wordpress***

    wordpress_database: wp_db
    wordpress_user: wp_user
    wordpress_password: CorkIgWac

    httpd_ssl_certificate_file: pu004.crt
    httpd_ssl_certificate_key_file: pu004.key


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
    Running test /vagrant/test/pu004/lamp.bats
     ✓ The necessary packages should be installed
     ✓ The Apache service should be running
     ✓ The Apache service should be started at boot
     ✓ The MariaDB service should be running
     ✓ The MariaDB service should be started at boot
     ✓ The SELinux status should be ‘enforcing’
     ✓ Web traffic should pass through the firewall
     ✓ Mariadb should have a database for Wordpress
     ✓ The MariaDB user should have "write access" to the database
     ✓ The website should be accessible through HTTP
     ✓ The website should be accessible through HTTPS
     ✓ The certificate should not be the default one
     ✓ The Wordpress install page should be visible under http://192.0.2.50/wordpress/
     ✓ MariaDB should not have a test database
     ✓ MariaDB should not have anonymous users

    15 tests, 0 failures

## Resources

[MariaDB](https://galaxy.ansible.com/bertvv/mariadb/)

[HTTPD](https://galaxy.ansible.com/bertvv/httpd/)

[Wordpress](https://galaxy.ansible.com/bertvv/wordpress/)

[firewalld_module](http://docs.ansible.com/ansible/firewalld_module.html)

[LAMP](https://www.liquidweb.com/kb/what-is-a-lamp-stack/)
