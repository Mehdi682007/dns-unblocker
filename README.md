# Dnsmasq SNIproxy One-click Install

### Script Overview:

* Principle: Use the DNS function of [Dnsmasq](http://thekelleys.org.uk/dnsmasq/doc.html) to redirect the DNS resolution of a specific website to the [SNIproxy](https://github.com/legendary1205/dns-unblocker) reverse proxy.

* Purpose: To break the restrictions on VPSs with limited access to streaming media, the prerequisite is to have a VPS that can stream media.

* Features: The script unblocks `Netflix Hulu HBO`[etc.](https://github.com/legendary1205/dns-unblocker/blob/master/proxy-domains.txt) by default. If you need to add or delete streaming domains, please edit the files `/etc/dnsmasq.d/custom_netflix.conf` and `/etc/sniproxy.conf`

* Script support system: CentOS7+, Debian9+, Ubuntu18+

* If the IP displayed by the script at the end does not match the actual public network IP, please modify the IP address in the file `/etc/sniproxy.conf`

### Script usage:

bash dnsmasq_sniproxy.sh [-h] [-i] [-f] [-id] [-fd] [-is] [-fs] [-u] [-ud] [-us]

-h , --help Display help information

-i , --install Install Dnsmasq + SNI Proxy
-f, --fastinstall Fast installation of Dnsmasq + SNI Proxy
-id, --installdnsmasq Install only Dnsmasq
-fd, --installdnsmasq Fast installation of Dnsmasq
-is, --installsniproxy Install only SNI Proxy
-fs, --fastinstallsniproxy Fast installation of SNI Proxy
-u, --uninstall Uninstall Dnsmasq + SNI Proxy
-ud, --undnsmasq Uninstall Dnsmasq
-us, --unsniproxy Uninstall SNI Proxy

### Fast installation (recommended):
``` Bash
wget --no-check-certificate -O dnsmasq_sniproxy.sh https://raw.githubusercontent.com/legendary1205/ddns-unblocker/main/dnsmasq_sniproxy.sh && bash dnsmasq_sniproxy.sh -f
```

### Normal installation:
``` Bash
wget --no-check-certificate -O dnsmasq_sniproxy.sh https://raw.githubusercontent.com/legendary1205/dns-unblocker/main/dnsmasq_sniproxy.sh && bash dnsmasq_sniproxy.sh -i
```

### Uninstallation method:
``` Bash
wget --no-check-certificate -O dnsmasq_sniproxy.sh https://raw.githubusercontent.com/legendary1205/dns-unblocker/main/dnsmasq_sniproxy.sh && bash dnsmasq_sniproxy.sh -u
```

### Usage method:
Set the DNS address of the proxy host to the host IP where dnsmasq is installed. If you encounter problems, try to keep only one in the configuration file DNS address.

To prevent abuse, it is recommended not to make the IP address public and use a firewall to restrict access appropriately.

### Debugging and troubleshooting:

- Confirm the running status of sniproxy

Check the status of sniproxy: `systemctl status sniproxy`

If sniproxy is not running, check whether other services are occupying ports 80 and 443, causing port conflicts. You can use the `netstat -tlunp | grep 443` command to view the port monitoring status.

- Confirm the firewall settings

Make sure that the firewall has opened ports 53, 80, and 443. When debugging, you can turn off the firewall: `systemctl stop firewalld.service`

For cloud service providers such as Alibaba Cloud, Tencent Cloud, and AWS, the port settings of the security group also need to be opened.

Test with another server: `telnet 1.2.3.4 53`

- Domain name resolution test

After configuring DNS, perform a domain name resolution test: `nslookup netflix.com` to check if the IP is the IP of the Netflix proxy server.
If the system does not have the nslookup command, you can install it on CentOS: `yum install -y bind-utils` Install on Ubuntu and Debian: `apt-get -y install dnsutils`

- Solve the problem of systemd-resolve service occupying port 53

Use `netstat -tlunp | grep 53` to find that port 53 is occupied by systemd-resolved
Modify the `/etc/systemd/resolved.conf` file:
```
[Resolve]
DNS=8.8.8.8 1.1.1.1 #Uncomment and add dns
#FallbackDNS=
#Domains=
#LLMNR=no
#MulticastDNS=no
#DNSSEC=no
#Cache=yes
DNSStubListener=no #Uncomment and change yes to no
```
Then execute the following command and restart the systemd-resolved service:
```
ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
 systemctl restart systemd-resolved.service
 ```
