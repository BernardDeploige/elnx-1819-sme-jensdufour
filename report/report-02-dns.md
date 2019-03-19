# Enterprise Linux Lab Report

- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

The following report will explain the installation and configuration of a DNS-server with BIND on `pu001`.

## Test plan

### pu001
* Start the machine pu001 from a terminal with following command: `vagrant up pu001`
* Connect with the virtuele machine through SSH: `vagrant ssh pu001`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`

### pu002
* Start the machine pu002 from a terminal with following command: `vagrant up pu002`
* Connect with the virtuele machine through SSH: `vagrant ssh pu002`
* Run following command to run the automated tests: `sudo /vagrant/test/runbats.sh`

## Procedure/Documentation

**Add server `pu001`/`pu002` as a `Vagrant` host:**

By adding `pu001`/`pu002` to `vagrant-hosts.yml`, `Vagrant` knows that it needs to create the virtual machine. 
The variables underneath also set the IP for the virtual machine. 

	- name: pu001
	  ip: 192.0.2.10
	- name: pu002
	  ip: 192.0.2.11

**Configure `pu001`/`pu002` as a `Vagrant` host**

This is our `Ansible Playbook`, in this file we will give the variables for every specific server. 
To configure the necessary services, we will use existing `Ansible` roles.

***Add the necessary `Ansible` roles to the `Ansible Playbook`***

	- hosts: pu001, pu002
	sudo: true
	roles:
	- bertvv.rh-base
	- bertvv.bind

**Configure the required services for `pu001`/`pu002`**

Make the file `pu001.yml`/`pu002` in the folder `ansible/host-vars/`. 
The variables in this file are only applied to the server `pu001`/`pu002` if `Ansible` is ran against it. 
It is used to configure the `Ansible Roles` that were defined earlier in the `Ansible Playbook`.

***Add the variables for the addition of DNS to the FireWall***

	rhbase_firewall_allow_services:
	  - dns

***Configure the IP of the Master DNS Server***

	bind_zone_master_server_ip: 192.0.2.10

***Configure the list of hosts that are allowed to query the DNS Server***

	bind_allow_query:
		- '192.0.2.0/24'
		- '172.16.0.0/16'

***Configure the DNS Server to listen on all interfaces***

	bind_listen_ipv4:
	  - any

***Configure the required domains***

	bind_zone_domains:
		- name: 'avalon.lan'
			networks:
				- '192.0.2'
				- '172.16'
			name_servers:
				- pu001
				- pu002

***Configure the required hosts as described in the initial assignment***

			hosts:
				- name: r001
					ip:
						- 192.0.2.254
						- 172.16.255.254
					aliases:
						- gw
				- name: pu001
					ip: 192.0.2.10
					aliases:
						- ns1
				- name: pu002
					ip: 192.0.2.11
					aliases:
						- ns2
				- name: pu003
					ip: 192.0.2.20
					aliases:
						- mail
				- name: pu004
					ip: 192.0.2.50
					aliases:
						- www
				- name: pr001
					ip: 172.16.0.2
					aliases:
						- dhcp
				- name: pr002
					ip: 172.16.0.3
					aliases:
						- directory
				- name: pr010
					ip: 172.16.0.10
					aliases:
						- inside
				- name: pr011
					ip: 172.16.0.11
					aliases:
						- files
***Configure the `MX Record Pointing` for the Mail Server***

			mail_servers:
				- name: pu003
					preference: '10'

## Test report

After following the test plan the following output should be returned:

### pu001

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

	Running test /vagrant/test/pu001/masterdns.bats
	 ✓ The `dig` command should be installed
	 ✓ The main config file should be syntactically correct
	 ✓ The forward zone file should be syntactically correct
	 ✓ The reverse zone files should be syntactically correct
	 ✓ The service should be running
	 ✓ Forward lookups public servers
	 ✓ Forward lookups private servers
	 ✓ Reverse lookups public servers
	 ✓ Reverse lookups private servers
	 ✓ Alias lookups public servers
	 ✓ Alias lookups private servers
	 ✓ NS record lookup
	 ✓ Mail server lookup

	13 tests, 0 failures


### pu002

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

	Running test /vagrant/test/pu002/slavedns.bats
	 ✓ The `dig` command should be installed
	 ✓ The main config file should be syntactically correct
	 ✓ The server should be set up as a slave
	 ✓ The server should forward requests to the master server
	 ✓ There should not be a forward zone file
	 ✓ The service should be running
	 ✓ Forward lookups public servers
	 ✓ Forward lookups private servers
	 ✓ Reverse lookups public servers
	 ✓ Reverse lookups private servers
	 ✓ Alias lookups public servers
	 ✓ Alias lookups private servers
	 ✓ NS record lookup
	 ✓ Mail server lookup

	14 tests, 0 failures




## Resources

[BIND](https://galaxy.ansible.com/bertvv/bind/)

[DNS Example](https://github.com/bertvv/ansible-role-bind/blob/docker-tests/test.yml)
