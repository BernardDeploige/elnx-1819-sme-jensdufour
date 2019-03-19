# Enterprise Linux Lab Report

- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

Basic server setup which meets following requirements:

- The EPEL repository must be installed (Extra Packages for Enterprise Linux, a third-party package repository)
- The following packages must be installed on all servers
    - bash-completion
    - bind-utils
    - git
    - nano
    - tree
    - vim-enhanced
    - wget
- Create a user account for yourself. Take your first name in lowercase. This user will become "administrator" of the system, which means `sudo` rights in practice.
- Generate an ssh key pair on your *host system* and install the public key on every server in your user account. You should be able to log into your servers without having to enter a password.
- Use the option to generate a custom `/etc/motd` file that shows network connection information after login.

## Test plan

* Start the machine pu004 from a terminal with following command: `vagrant up pu004`
* Connect with the virtuele machine through SSH: `vagrant ssh pu004`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`

This automated test is build using BATS. It is responsible for testing the following aspects of the server:

* EPEL repository should be available
* Bash-completion should have been installed
* bind-utils should have been installed
* Git should have been installed
* Nano should have been installed
* Tree should have been installed
* Vim-enhanced should have been installed
* Wget should have been installed
* Admin user `jens` should exist
* Custom /etc/motd should have been installed


## Procedure/Documentation

**Make an `all.yml` in the folder `ansible/group-vars/`**

This file is used to set variables that will be used for every host that Ansible is ran against.

**Add `rhbase_repository` role to the file**

This is an Ansible role for basic setup of a server with a RedHat-based Linux distribution (CentOS, Fedora, RHEL, ...).

      rhbase_repositories:
            - epel-release

**Install the required packages**

The addition of the following variables will add the required packages to every server that Ansible is ran against.

      rhbase_install_packages:
          - bash-completion
          - bind-utils
          - git
          - nano
          - tree
          - vim-enhanced
          - wget

**Add a user to the server**

In this example we will add the user `jens`. This can be changed. The chosen password here is `jens`. 
This was generated with [MKPasswd](https://www.mkpasswd.net/index.php), `sha512-crypt` is the chosen encryption.

      rhbase_users:
           - name: jens
             comment: "Administrator"
             groups:
               - wheel
             password: '$6$9vnZFKnNLSpBOnEg$P9.odnoUisUWIbakF/XMAL1/g4kRdBaQvsEULPy5WsxSR.N2preY8n6oqQDHvXai8R0I7EjJOO9RfGktU/MOZ1'


**Secure the connection between the host and server**

The following command was run on the host server. The generated keys are used to secure the connection between the host device and server:

      ssh-keygen

The following variable was than added to secure the connection:

      rhbase_ssh_key: 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDeD2VavNudHiRUQVMDO+fhrssLQ3qRHcpgoG2gkAYX0BUxzxkPdxwBy0oCGtHPWRYIc2g5tp7VyYdyKNC1EgDMTerxm/Fs/ksIKc2mJSoxIAwIMFhqdqmtx0XvNheCX1QGRM0Wx5fXD2Qnqx4hVVcmV+NY/5L9Pb/a2AqlSH4BXJt/Yd1tbY14kedQ36siLVk6vQ3U+x+pWFyGI3MAnicjW/nyIHb8VEDmJ+zDExut0ku2iMyYP/CwQf5ltEVlDxbNqrfGDQ5f33mJ215/lIPIh8W7AqdKwz2qARI73rFS7SYmI7GXhoAQt49/aFiGdapgls2qmkSjsnvTvd6/w2CB jensdufour@Lenovo'

**Upgrade a user to `Administrator`**

      rhbase_ssh_user: jens

**Allow a MOTD (Message Of The Day)**

      rhbase_motd: true

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

## Resources

[rh-base](https://galaxy.ansible.com/bertvv/rh-base/)

[Ansible](http://docs.ansible.com/ansible/become.html)

[MKPasswd](https://www.mkpasswd.net/index.php)
