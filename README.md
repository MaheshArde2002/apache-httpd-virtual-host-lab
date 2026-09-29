# Apache HTTP Server Deployment & VirtualHost Configuration

A hands-on Linux system administration project demonstrating Apache HTTP Server deployment and VirtualHost configuration on CentOS 8 / RHEL 8.

## 📌 Project Overview

This project covers:

* Apache HTTP Server installation
* Apache service management
* Custom website directory
* VirtualHost configuration
* Firewalld configuration
* Local domain resolution
* HTTP testing and verification
* Basic Apache troubleshooting

## 🖥️ Environment

| Component    | Details                      |
| ------------ | ---------------------------- |
| OS           | CentOS 8 / RHEL 8            |
| Web Server   | Apache HTTP Server (`httpd`) |
| Protocol     | HTTP                         |
| Port         | 80                           |
| Firewall     | Firewalld                    |
| DocumentRoot | `/var/www/html/Apache.com`   |
| Domain       | `ApacheIntro.com`            |
| Network      | Local / LAN                  |
| IP           | DHCP                         |

> **Note:** The server uses DHCP, so the IP address may change. Use `hostname -I` to find the current IP.

## 📂 Project Structure

```text
apache-httpd-virtual-host-lab/
├── README.md
├── config/
│   └── httpd-vhosts.conf
├── website/
│   └── index.html
├── troubleshooting/
│   └── troubleshooting.md
└── screenshots/
```

## ⚙️ Apache Installation

Check Apache:

```bash
rpm -qa | grep -i httpd
```

Install Apache:

```bash
yum install httpd-* -y
```

Start and enable Apache:

```bash
systemctl start httpd
systemctl enable httpd
systemctl status httpd
```

## 🌐 Website Configuration

Create the website directory:

```bash
mkdir -p /var/www/html/Apache.com
```

Create the webpage:

```bash
vim /var/www/html/Apache.com/index.html
```

Example:

```html
<h1>Welcome to ApacheIntro.com</h1>
<p>Apache HTTP Server is working successfully.</p>
```

## 🔧 VirtualHost Configuration

Create a backup:

```bash
cp /etc/httpd/conf.d/httpd-vhosts.conf \
/etc/httpd/conf.d/httpd-vhosts.conf.bak
```

Edit the configuration:

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

Configuration:

```apache
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName ApacheIntro.com
    ServerAlias www.ApacheIntro.com

    DocumentRoot "/var/www/html/Apache.com"

    ErrorLog "/var/log/httpd/apacheintro-error_log"
    CustomLog "/var/log/httpd/apacheintro-access_log" combined

    <Directory "/var/www/html/Apache.com">
        AllowOverride None
        Require all granted
    </Directory>
</VirtualHost>
```

Test the configuration:

```bash
httpd -t
```

Expected:

```text
Syntax OK
```

Restart Apache:

```bash
systemctl restart httpd
```

## 🔥 Firewalld

Allow HTTP traffic:

```bash
firewall-cmd --permanent --add-service=http
firewall-cmd --reload
firewall-cmd --list-all
```

## 🌍 Find Server IP

```bash
hostname -I
```

Use the current IP address for testing.

## 🖥️ Local Domain Resolution

### Linux

Edit:

```bash
sudo vim /etc/hosts
```

Add:

```text
YOUR_SERVER_IP    ApacheIntro.com
YOUR_SERVER_IP    www.ApacheIntro.com
```

### Windows

Open Notepad **as Administrator** and edit:

```text
C:\Windows\System32\drivers\etc\hosts
```

Add:

```text
YOUR_SERVER_IP    ApacheIntro.com
YOUR_SERVER_IP    www.ApacheIntro.com
```

## 🧪 Testing

Test Apache locally:

```bash
curl http://localhost
```

Test using the server IP:

```bash
curl http://YOUR_SERVER_IP
```

Test the VirtualHost:

```bash
curl http://ApacheIntro.com
```

Open in a browser:

```text
http://ApacheIntro.com
```

## 🔍 Verification

Check port 80:

```bash
ss -lntp | grep :80
```

Check Apache processes:

```bash
pgrep -a httpd
```

Check VirtualHost:

```bash
httpd -S
```

Check logs:

```bash
tail -f /var/log/httpd/apacheintro-error_log
```

## 🛠️ Troubleshooting

For common Apache service, configuration, port, Firewalld, VirtualHost, permissions, and hostname-resolution problems, see:

`troubleshooting/troubleshooting.md`

## 📚 Skills Demonstrated

* Linux System Administration
* Apache HTTP Server
* VirtualHost Configuration
* Systemd
* Firewalld
* HTTP / Port 80
* Linux & Windows Hosts File
* Web Server Troubleshooting
* Basic Networking
* Apache Logs
* `curl`

## 🎯 Project Objective

Deploy a functional Apache web server, configure a custom VirtualHost, allow HTTP traffic through Firewalld, and access the website using a local domain name.
