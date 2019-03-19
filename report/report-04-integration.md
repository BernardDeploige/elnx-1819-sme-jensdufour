# Enterprise Linux Lab Report

- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

The following report will explain the installation and configuration of a DHCP server for a small network and a router with VyOS. After this the integration of the entire network will be tested via a Workstation.

## Test plan

* Start all the servers using the following command from the main folder: `vagrant up`
* Create a new VM, in VirtualBox, for Fedora
* Mount the Fedora Live ISO on the VM 
* Configure two host-only adapters on the VM
* Open the VM using VirtualBox
* Verify that DHCP gives both interfaces an IP (One reserved: 172.16.128.5; One dynamic): `ip a`
* Verify the connection to `pr001` using the following command: `ping 172.16.0.2`
* Verify the DNS by issuing a ping to the hostname of `pu001`: `ping http://www.avalon.lan`
* Verify the HTTP Server via the [hostname](www.avalon.lan) by surfing to it via the built-in browser
* Verify the FTP Server via the [hostname](ftp://www.files.avalon.lan) by surfing to it via the built-in browser
## Procedure/Documentation

### DHCP Server

**Add server `pr001` as a `Vagrant` host**

By adding pr001 to `vagrant-hosts.yml`, `Vagrant` knows that it needs to create the virtual machine. The variables underneath also set the IP for the virtual machine. 

    - name: pr001
      ip: 172.16.0.2
      netmask: 255.255.0.0

**Configure pr001 in `ansible/site.yml`**

This is our `Ansible Playbook`, in this file we will give the variables for every specific server. To configure the necessary services, we will use existing `Ansible` roles.

***Add the necessary `Ansible` roles to the `Ansible Playbook`***

    - hosts: pr001
      sudo: true
      roles:
        - bertvv.rh-base
        - bertvv.dhcp

***Install the required packages for DHCP***

    rhbase_install_packages:
      - dhcp

***Add the variables for the addition of DHCP to the FireWall***

    rhbase_firewall_allow_services:
      - dhcp

***Configure the DHCP Domain Name***

    dhcp_global_domain_name: avalon.lan

***Configure the DHCP Routers***

    dhcp_global_routers: 172.16.255.254

***Configure the DNS Servers for DHCP***

    dhcp_global_domain_name_servers:
      - 192.0.2.10
      - 192.0.2.11

***Configure the required DHCP subnets***

    dhcp_subnets:
      - ip: 172.16.0.0
        netmask: 255.255.0.0
        domain_name_servers: 172.16.255.254
        pools:
          - default_lease_time: 43200
            range_begin: 172.16.192.1
            range_end: 172.16.255.253
            allow: unknown-clients

***Configureer the required DHCP hosts***

    dhcp_hosts:
      - name: client
        mac: '08:00:27:94:B9:37'
        ip: 172.16.128.5

### Router

The file `router-config.sh` in the folder `scripts`, is responsible for the configuration of the router. It used to provision the VM with VyOS, using `Vagrant` and `Ansible`.

***Start the configuration mode on the VyOS Router***

    source /opt/vyatta/etc/functions/script-template

    configure

***Configure the basic settings***

    set system host-name 'router'
    set system domain-name 'avalon.lan'
    set service ssh port '22'

***Configure the required IP's***

    set interfaces ethernet eth0 address dhcp
    set interfaces ethernet eth0 description "WAN link"

    set interfaces ethernet eth1 address 192.0.2.254/24
    set interfaces ethernet eth1 description DMZ

    set interfaces ethernet eth2 address 172.16.255.254/16
    set interfaces ethernet eth2 description internal

***Configure NAT***

    set nat source rule 200 outbound-interface 'eth1'
    set nat source rule 200 translation address 'masquerade'
    set nat source rule 200 source address '172.16.0.0/16'

    set nat source rule 300 outbound-interface 'eth0'
    set nat source rule 300 translation address 'masquerade'
    set nat source rule 300 source address '172.16.0.0/16'

***Configure the NTP Server***

    delete system ntp server 0.pool.ntp.org
    delete system ntp server 1.pool.ntp.org
    delete system ntp server 2.pool.ntp.org
    set system ntp server 'be.pool.ntp.org'
    set system time-zone Europe/Brussels

***Configure the DNS Server***

    set service dns forwarding domain avalon.lan server 192.0.2.10
    set service dns forwarding name-server 10.0.2.3
    set service dns forwarding listen-on 'eth1'
    set service dns forwarding listen-on 'eth2'

***Make the Router configuration persistent***

    commit
    save

***Configure the required permissions***

    sudo chown -R root:vyattacfg /opt/vyatta/config/active

### Required changes to `pr001` for the designation of DNS to the Fedora Client

***Add a DNS interface that points to the VyOS Router***

    dns_intname: "eth1"
    dns_nameservers:
    - DNS1=172.16.255.254
    dns_search: avalon.lan

***Add the variables for the addition of DHCP to the FireWall***

    rhbase_firewall_allow_services:
    - dhcp

***Change the DHCP lifetime to 24 hours***

    dhcp_global_default_lease_time: 86400
    dhcp_global_max_lease_time: 86400

***Configure the Router as the only DNS Server***

    dhcp_global_domain_name_servers:
        - 172.16.255.254

## Test report

After following the test plan the following output should be returned:

* Start all the servers using the following command from the main folder: `vagrant up`
* Create a new VM, in VirtualBox, for Fedora
* Mount the Fedora Live ISO on the VM 
* Configure two host-only adapters on the VM
* Open the VM using VirtualBox
* Verify that DHCP gives both interfaces an IP (One reserved: 172.16.128.5; One dynamic): `ip a`
    * ![DHCP](https://i.imgur.com/Lwjlsrj.png "DHCP")
* Verify the connection to `pr001` using the following command: `ping 172.16.0.2`
    * ![PING_PR001](https://i.imgur.com/HuRTHQa.png "PING_PR001")
* Verify the DNS by issuing a ping to the hostname of `pu001`: `ping http://www.avalon.lan`
* Verify the HTTP Server via the [hostname](www.avalon.lan) by surfing to it via the built-in browser
    * ![Webserver](https://i.imgur.com/kXIMoZa.png "Webserver")
* Verify the FTP Server via the [hostname](ftp://www.files.avalon.lan) by surfing to it via the built-in browser
    * ![FIREFOX](https://i.imgur.com/06OQqMF.png "FIREFOX")


## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.


[VyOS Wiki](https://wiki.vyos.net/wiki/Main_Page)

[Fedora](https://getfedora.org/nl/)
