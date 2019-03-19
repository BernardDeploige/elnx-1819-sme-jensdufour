# Enterprise Linux Lab Report - Troubleshooting

- Student name: Jens Du Four
- Class/group: TIN-TI-3B (Gent)

## Instructions

- Write a detailed report in the "Report" section below (in Dutch or English)
- Use correct Markdown! Use fenced code blocks for commands and their output, terminal transcripts, ...
- The different phases in the bottom-up troubleshooting process are described in their own subsections (heading of level 3, i.e. starting with `###`) with the name of the phase as title.
- Every step is described in detail:
    - describe what is being tested
    - give the command, including options and arguments, needed to execute the test, or the absolute path to the configuration file to be verified
    - give the expected output of the command or content of the configuration file (only the relevant parts are sufficient!)
    - if the actual output is different from the one expected, explain the cause and describe how you fixed this by giving the exact commands or necessary changes to configuration files
- In the section "End result", describe the final state of the service:
    - copy/paste a transcript of running the acceptance tests
    - describe the result of accessing the service from the host system
    - describe any error messages that still remain

## Checklist physical and network access layer
1. Staat het aan?
	- Physical network interfaces were not found

		=> Virtualbox - Instellingen - Netwerk - Geavanceerd
			Stel een host-only adapter in op vboxnet0
            
2. Is de netwerkkabel ingeplugd? 
        =>Virtualbox - Instellingen - Netwerk - Geavanceerd - Cable Connected
3. Zijn de Ethernet poort LED's aan?
4. Gebruik het commando `ip link` om te zien of de adapters 'Up' of 'No carrier' zijn.
​

**Verwacht**

- enp0s3 up
- enp0s8 up

**Resultaat**

- enp0s3 up
- enp0s8 up
​
## Checklist Internet Layer
### IP-address
1. Kijk in de ifcfg-IFACE of IP-adres via DHCP of manueel is ingesteld. Bij DHCP kijk je of Device de juiste naam heeft, of ONBOOT op 'yes' staat en dat BOOTPROTO op 'DHCP' staat. Als het manueel ingesteld is, dan moeten IP-adres en Netmask ingevuld zijn. 

2. Kijken of je de juiste ip-adressen hebt gekregen. Op VB is heeft een NAT-adapter een ip-adres 10.0.2.15/24 en een host-only adapter 192.168.56.0/24. DHCP begint uit te delen op 192.168.56.101.
### Default gateway
Elke host op de LAN moet zijn router kennen. 
1. No default set => probleem met IP adres. als dhcp is =>    verkeerd geconfigureerd.
### DNS Server
Om ip-adressen om te zetten naar hostnames of omgekeerd, is er DNS Server nodig! Je kan dit checken door naar de resolv.conf te kijken. 
Door dit alleen kan je niet zien of je DNS Server ech beschikbaar is. Je zal 'dig' en nslookup' moeten gebruiken.
​
**Verwacht**

- enp0s3 : 10.0.2.15/24
- enp0s8: 192.168.56.42/24
- Default gateway: 10.0.2.0/24
- DNS server 10.0.2.3

**Resultaat**

- enp0s3: 10.0.2.15/24 (OK)
- enp0s8: Heeft geen IPv4 (NOK) ==> Typfout in config
- Default gateway: 10.0.2.2 (OK)
- DNS server 10.0.2.3 (OK)

**Troubleshoot Internet Layer**
- Verander IPADRR naar IPADDR in `sudo vi /etc/sysconfig/network-scripts/ifcfg-enp0s8`
​
## Checklist transport layer
In dit stukje gaan we bekijken of de service zelf aan het runnen is en welke poort het gebruikt. Hier voor httpd, maar kan voor alle services.
1. Is de service aan het lopen?
2. Welke poort gebruikt de service?
3. Laat de firewall het verkeer toe op de services?
​

**Verwacht**

- DNS service draait (NOK)
- Nodige hosts zijn vermeld (OK)
- Webservice draait (NOK)
- DHCP server draait (OK)
- Firewall laat connecties door

**Resultaat**

- DNS service draait niet: *Active: inactive (dead)*
- nginx is aanwzeig (1)
- nginx.service draait niet: *Active: inactive (dead)* (2)
- DHCP server draait *active (running)*
- Firewall check port 80 (OK) en Port 443 (NOK)

**Troubleshoot Transport Layer**

- DNS Service starten: `sudo systemctl start dnsmasq.service`
- Enable DNS Service: `sudo systemctl enable dnsmasq.service`
- Typfout in nginx config: `sudo vi /etc/nginx/nginx.conf`
    - Wijzig bij de ninxg.pem naar nginx.pem
    - Wijzig de poort voor HTTPS van :8443 naar :443
- NGINX starten: `sudo systemctl start nginx`
- NGINX enablen: `sudo systemctl enable nginx`
- HTTPS Service toevoegen aan de firewall: `firewall-cmd --permanent --add-service=https`
- Herstart de Firewall: `sudo firewall-cmd --reload`
- Voeg de benodigde poorten toe:
    - `sudo firewall-cmd --add-port=80/tcp`
    - `sudo firewall-cmd --add-port=80/tcp --permanent`
    - `sudo firewall-cmd --add-port=443/tcp`
    - `sudo firewall-cmd --add-port=443/tcp --permanent`
    ​
## Checklist application layer
​
1. Check de log files.
2. Kijk of de configuratie van de applicatie correct is.
3. Is de service beschikbaar voor clients?

**Resultaat**
- NGINX server kan niet opstarten: `sudo systemctl start nginx`


**Troubleshoot Application Layer**

- Bekijk de foutmelding van NGINX: `sudo journalctl -f -u nginx.service`
- Bekijk de error log van NGINX  `sudo cat /var/log/nginx/error.log`
    - nigxn.pem i.p.v. nginx.pem (`ls /etc/pki/tls/certs/`)
        - Aanpassen via Vi: `sudo vi /etc/nginx.conf`
- NGINX herstarten: `sudo systemctl restart nginx.service`

## Checklist SELinux

1. Controleer de booleans van httpd
2. Bekijk de context van de PHP-pagina
3. Verander de Permissions van de PHP-file

**Resultaat**
- Controleer de booleans van httpd: `getsebool -a | grep https_can_network --> off`
    - `httpd_can_network_connect --> off`
    - `httpd_can_network_connect_cobbler --> off`
    - `httpd_can_network_connect_db --> off`
    - `httpd_can_network_memcache --> off`
    - `httpd_can_network_relay --> off`
- Bekijk de context van de index.php `ls -Z /usr/share/nginx/html`

**Troubleshoot  SELinux**

- Laat httpd-verkeer toe: `sudo setsebool httpd_can_network_connect on`
- Verander de context zodat httpd aan de bestanden kan: `sudo chcon -t httpd_sys_content_t /usr/share/nginx/html/index.php`