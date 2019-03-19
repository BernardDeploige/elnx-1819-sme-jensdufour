# Cheat sheets and checklists
- Student name: Jens Du Four
- Github repo: <https://github.com/HoGentTIN/elnx-1819-sme-jensdufourhogent.git>

## Basic commands
| Task         | Command |
| :---         | :---  |
| Query IP-adress(es) | `ip a` |
| Show logs in realtime| `journalctrl -f`|
|Put system to azerty|`sudo localectl set-keymap be`
||`sudo localectl set-x11-keymap be`|
​
​
​
## Git workflow
Simple workflow for a personal project without other contributors:​

| Task         | Command |
| :---         | :---  |
| Current project status            | `git status`       |
| Select files to be committed         | `git add FILE...`     |
| Commit changes to local repository      | `git commit -m 'MESSAGE'` |
| Push local changes to remote repository   | `git push`        |
| Pull changes from remote repository to local | `git pull` |
​
## Physical/Network Access Layer
1. Control power state of devices
2. Is the network cable correctly connected?
3. Are the LEDs on the Ethernetport indicating a correct connection?
4. Use `ip link` to check if the adapters are `up` or `no carrier` 
​
#### Commands
| Task                     | Command          |
| :---                     | :---           |
|Verify that all interfaces are `up` | `ip link` |
|Ping an IP adress for # times |`ping -c # <ip-address>`|
​
## Checklist internet layer
### IP-address
1. Check the `ifcfg-IFACE` or `IP-adress` is set via `DHCP` or manual. 

    * In the DHCP configuration check if the Device has the right name.
    * Also make sure `ONBOOT` is set to `yes` and `BOOTPROTO` is set to `DHCP`. 
    * If the configuration is set to manual, than make sure the IP and netmask is given. 
2. Check if the interfaces are given the correct IP-address. 
### Default gateway
Every host inside the `LAN` must now it's router. 
* `NO DEFAULT SET`:
    * Make sure the IP-address is correct
    * If using `DHCP` make sure it is configured correctly.
### DNS Server
DNS Server is needed to convert an `IP-address` to a `hostname`.
   * Make sure a `resolv.conf` is provided.
   * Make sure the `DNS Server` is available with:
       * `dig`
       * `nslookup`​
#### Commands
## Checklist transport layer
In dit stukje gaan we bekijken of de service zelf aan het runnen is en welke poort het gebruikt. Hier voor httpd, maar kan voor alle services.
1. Is de service aan het lopen?
2. Welke poort gebruikt de service?
3. Laat de firewall het verkeer toe op de services?
#### Commands
| Task                                            | Command |
|------------------------------------------------|-----------------------------------|
| Check the `ifconfig` of a specific interface | `cat /etc/sysconfig/network-scripts/ifcfg-eth0` |         |
|Check Apache-service |`sudo systemctl status httpd.service`|
|Check NGINX-service |`sudo systemctl status nginx.service`|
|Start Apache-service|`sudo systemctl start httpd.service`|
|Start NGINX-service|`sudo systemctl start nginx.service`|
|Start service on boot|`sudo systemctl enable httpd.service`|
|Show ports used by service|`sudo ss -tlnp`|
|Change `httpd.conf` for `listen ports`|`sudo vi /etc/httpd/conf/httpd.conf`|
|Check the ports for known services|`cat /etc/services`|
|Check services allowd by FireWall|`sudo firewall-cmd --list-all`|
|Voeg een service toe die de firewall toelaat|`sudo firewall-cmd --permanent --add-service=httpd`|
|Reloard the FireWall|`sudo firewall-cmd --reload`|
|Allow an interface in the FireWall|`sudo firewall-cmd --add-interface=eth1 --permanent`|
|Allow a port in the FireWall|`sudo firewall-cmd --add-port=80/tcp`|
||`sudo firewall-cmd --add-port=80/tcp --permanent`|
||`sudo firewall-cmd --add-port=443/tcp`|
||`sudo firewall-cmd --add-port=443/tcp --permanent`|
## Checklist Application Layer​
1. Check the log
2. Verify the configuration of the application
3. Verify that the service is available for users?
#### Commands

| Task                     | Command          |
| :---                     | :---           |
|Check relevant logs and changes|`sudo journalctl -f -u httpd.service`|
|Check the Apache-service errorlog|`sudo tail -f /var/log/httpd/error_log`|
|Verify the Apache-serivce config|`cat /etc/httpd/httpd.conf`|
|Verify Apache syntax| `apachectl configtest`|
|Restart Apache-service|`sudo systemctl restart httpd.service`|
|Scan HTTP(S) ports|`sudo nmap -sS -p 80,443 HOST`|
​
## Checklist SELinux​
#### Commands

| Task                     | Command          |
| :---                     | :---           |
|Verify booleans|`getsebool -a`|
|Change boolean Apache-service|`sudo setsebool httpd_can_network_connect_db on`|
|Allow Apache-service|`sudo setsebool httpd_can_network_connect on`|
|Verify the `test.php` context|`ls -Z /var/www/html/test.php`|
|Allow access from Apache-service|`sudo chcon -t httpd_sys_content_t /var/www/html/test.php`|
​
