# Dnsmasq SNIproxy One-click Install

### Script Overview:

* **How it works:**  
  Utilizes [Dnsmasq](http://thekelleys.org.uk/dnsmasq/doc.html) for DNS resolution and redirects certain domain queries to [SNIproxy](https://github.com/legendary1205/dns-unblocker) for reverse proxying.

* **Use Case:**  
  Helps VPS instances that are restricted from accessing streaming services to bypass those restrictions — assuming you have another VPS that *can* access streaming platforms.

* **Features:**  
  The script unlocks streaming services like `Netflix`, `Hulu`, `HBO`, [and more](https://github.com/legendary1205/dns-unblocker/blob/master/proxy-domains.txt) by default.  
  To modify or add domains, edit the files:  
  `/etc/dnsmasq.d/custom_netflix.conf` and `/etc/sniproxy.conf`.

* **Supported Systems:** CentOS 7+, Debian 9+, Ubuntu 18+  
  > If the IP displayed at the end of the installation doesn't match your public IP, manually update the IP in `/etc/sniproxy.conf`.

---

### Script Usage:

```bash
bash dnsmasq_sniproxy.sh [-h] [-i] [-f] [-id] [-fd] [-is] [-fs] [-u] [-ud] [-us]
  -h , --help                Show help information
  -i , --install             Install Dnsmasq + SNI Proxy
  -f , --fastinstall         Fast install Dnsmasq + SNI Proxy
  -id, --installdnsmasq      Install Dnsmasq only
  -fd, --fastdnsmasq         Fast install Dnsmasq only
  -is, --installsniproxy     Install SNI Proxy only
  -fs, --fastsniproxy        Fast install SNI Proxy only
  -u , --uninstall           Uninstall Dnsmasq + SNI Proxy
  -ud, --undnsmasq           Uninstall Dnsmasq only
  -us, --unsniproxy          Uninstall SNI Proxy only
```

---

### 🚀 Recommended Fast Install:
```bash
wget https://github.com/legendary1205/dns-unblocker/raw/main/dns_installer_linux_amd64 -O installer && chmod +x installer && ./installer -f
```

### 🛠 Standard Install:
```bash
wget https://github.com/legendary1205/dns-unblocker/raw/main/dns_installer_linux_amd64 -O installer && chmod +x installer && ./installer -i
```

### ❌ Uninstall:
```bash
wget https://github.com/legendary1205/dns-unblocker/raw/main/dns_installer_linux_amd64 -O installer && chmod +x installer && ./installer -u
```

---

### 📡 Usage Instructions:
Set the DNS address of your clients (devices/users) to the IP of the VPS where Dnsmasq is installed.  
If you run into issues, try keeping only one DNS entry in the config.

For security reasons, avoid exposing the server’s public IP and apply firewall rules to limit access.

---

### 🛠 Troubleshooting:

- **Check SNIproxy status:**

  ```bash
  systemctl status sniproxy
  ```

  If it’s not running, check if ports 80 or 443 are already in use:
  ```bash
  netstat -tlunp | grep 443
  ```

- **Check Firewall Rules:**

  Make sure ports 53, 80, and 443 are open.  
  To temporarily disable firewall for debugging:
  ```bash
  systemctl stop firewalld.service
  ```

  On cloud platforms (Alibaba, Tencent Cloud, AWS, etc.), make sure security group rules allow these ports too.

  Test from another server:
  ```bash
  telnet 1.2.3.4 53
  ```

- **DNS Query Test:**

  After setup, test domain resolution:
  ```bash
  nslookup netflix.com
  ```
  Result should point to the proxy IP.

  If `nslookup` is not found:
  - CentOS:
    ```bash
    yum install -y bind-utils
    ```
  - Ubuntu/Debian:
    ```bash
    apt-get -y install dnsutils
    ```

- **Fix systemd-resolved occupying port 53:**

  If this command:
  ```bash
  netstat -tlunp | grep 53
  ```
  shows port 53 used by `systemd-resolved`, then edit:
  `/etc/systemd/resolved.conf` and change:
  ```ini
  [Resolve]
  DNS=8.8.8.8 1.1.1.1 
  #FallbackDNS=
  #Domains=
  #LLMNR=no
  #MulticastDNS=no
  #DNSSEC=no
  #Cache=yes
  DNSStubListener=no  
  ```

  Then run:
  ```bash
  ln -sf /run/systemd/resolve/resolv.conf /etc/resolv.conf
  systemctl restart systemd-resolved.service
  ```
