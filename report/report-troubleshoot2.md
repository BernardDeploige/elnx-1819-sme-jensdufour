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

## Report

### Phase 1: Physical and Network Acces Layer

1. Zijn de fysieke netwerkinterfaces aangesloten?
		=> Virtualbox - Instellingen - Netwerk - Geavanceerd
			Stel een host-only adapter in op vboxnet0
            
2. Is de netwerkkabel ingeplugd? 
        =>Virtualbox - Instellingen - Netwerk - Geavanceerd - Cable Connected

3. Zijn de ethernetpoort LEDs aan?
4. Gebruik het commando `ip link` voor statuscontrole van interfaces.

**Verwacht**

- enp0s3 up
- enp0s8 up

**Resultaat**

- enp0s3 up
- enp0s8 down

**Oplossing**

    sudo ip link set dev enp0s8 up


### Phase 2: Internet Layer

1. IP address: ip a
    - IP address?
       - Kijk in de ifcfg-IFACE of IP-adres via DHCP of manueel is ingesteld. Bij DHCP kijk je of Device de juiste naam heeft, of ONBOOT op 'yes' staat en dat BOOTPROTO op 'DHCP' staat. Als het manueel ingesteld is, dan moeten IP-adres en Netmask ingevuld zijn. 
        - In VirtualBox heeft een NAT-adapter een ip-adres 10.0.2.15/24 en een host-only adapter 192.168.56.0/24. DHCP begint uit te delen op 192.168.56.101.
    - In correct subnet?
    - DHCP or fixed IP?
2. Default gateway: ip r
3. DNS service: /etc/resolv.conf
    - Door dit alleen kan je niet zien of je DNS Server ech beschikbaar is. Je zal 'dig' en nslookup' moeten gebruiken.

    **Gegeven**

        DEVICE=enp0s8
        ONBOOT=no
        BOOTPROTO=none
        IPADDR=192.168.56.42
        NETMASK=255.255.255.0
        
    **Oplossing**

        DEVICE=enp0s8
        ONBOOT=yes
        BOOTPROTO=none
        IPADDR=192.168.56.42
        NETMASK=255.255.255.0   


Het volgende commando is ook een oplossing tot als er herstart wordt.

        sudo ip link set dev enp0s8
### Phase 3: Transport Layer

1. Service running? 
    - sudo systemctl status named

    **Resultaat**
    Het starten van de service lukt niet wegens een foutmelding. Dis is een fout die we oplossen in de Application Layer.

    **Oplossing**


2. Correct port/inteface? 
    - sudo ss -tlpn
    - sudo ss -ulpn
    - cat /etc/services
3. Firewall settings?
    - sudo firewall-cmd --list-all
    - sudo firewall-cmd --add-service SERVICE
    - sudo firewall-cmd --add-service SERVICE --permanent
    - sudo firewall-cmd --reload

    **Resultaat**
    DNS wordt nog geblokkeerd door de firewall.

    **Oplossing**   

        sudo firewall-cmd --add-service dns
        sudo firewall-cmd --add-service dns --permanent
        sudo firewall-cmd --reload

### Phase 4: Application Layer
1. Check the logs. Other checks are application dependent
    - Read the reference manuals!
    - journalctl -f -u named.service
    - tail -f /var/log/named/error_log
2. Validate config file syntax
    - sudo named-checkconf /etc/named.conf
3. Use (command line) client tools
    - e.g. dig, nslookup, etc.


    **BIND Configuratie (/etc/named.conf)**

        Open de configuratie via volgend commando.

            sudo vim /etc/named.conf

    **In de configuratiefile**


        options {
            //listen-on port 53 { 127.0.0.1; };
            listen-on-v6 port 53 {any; };
            allow-query { any; };

            // ...
            }

    **Correcte versie**

        options {
            listen-on port 53 { any; };
            listen-on-v6 port 53 { any; };
            allow-query { any; };

            // ...
            }

    **Zone File: Reverse Lookup Zone**

    **Zone 192.0.2.0**

            sudo named-checkzone 2.0.192.in-addr.arpa /var/named/2.0.192.in-addr.arpa

            OK

    **Zone 192.168.56.0**
        
            sudo rm 56.168.192.in-addr.arpa
            sudo mv 192.168.56.in-addr.arpa 56.168.192.in-addr.arpa
            sudo named-checkzone 56.168.192.in-addr.arpa /var/named/56.168.192.in-addr.arpa

            OK


    **Zone File: Forward Lookup Zone**

        sudo named-checkzone cynalco.com /var/named/cynalco.com

        OK


Na deze stappen kan de service gestart worden aan de hand van volgend commando. 

    sudo systemctl start named


### Phase 5: SELinux Troubleshooting
- Settings:
    - Booleans: getsebool, setsebool
    - Contexts, labels: ls -Z, chcon, restorecon
    - Policy modules: sepolicy


## End result



## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.
