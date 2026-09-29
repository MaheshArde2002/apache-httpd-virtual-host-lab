# Apache HTTP Server Troubleshooting

This document covers common troubleshooting scenarios for the Apache HTTP Server (`httpd`) deployed on CentOS/RHEL 8.

The basic troubleshooting process is:

```text
Problem
   ↓
Check
   ↓
Identify the Cause
   ↓
Fix
   ↓
Verify
```

---

## 1. Apache Service Is Not Running

### Check

```bash
systemctl status httpd
```

### Start Apache

```bash
systemctl start httpd
```

### Enable at Boot

```bash
systemctl enable httpd
```

### Verify

```bash
systemctl status httpd
```

Expected:

```text
Active: active (running)
```

---

## 2. Apache Configuration Syntax Error

After modifying the VirtualHost configuration, always test the configuration:

```bash
httpd -t
```

Expected:

```text
Syntax OK
```

If an error appears, check:

* Incorrect directive
* Missing quotes
* Missing `</VirtualHost>`
* Incorrect file path
* Typographical errors

Configuration file:

```bash
vim /etc/httpd/conf.d/httpd-vhosts.conf
```

---

## 3. Apache Is Not Listening on Port 80

Check whether Apache is listening on HTTP port 80:

```bash
ss -lntp | grep :80
```

Also check Apache:

```bash
systemctl status httpd
```

If necessary:

```bash
systemctl restart httpd
```

Then verify again:

```bash
ss -lntp | grep :80
```

---

## 4. Firewalld Is Blocking HTTP

Check the firewall:

```bash
firewall-cmd --list-all
```

Allow HTTP:

```bash
firewall-cmd --permanent --add-service=http
```

Reload:

```bash
firewall-cmd --reload
```

Verify:

```bash
firewall-cmd --list-services
```

`http` should appear in the allowed services.

---

## 5. Website Is Not Loading by IP

First check the current IP address.

Because this lab uses DHCP, the IP can change:

```bash
hostname -I
```

Example:

```text
192.168.0.105
```

Use the current IP:

```text
http://YOUR_SERVER_IP
```

Then check:

```bash
systemctl status httpd
```

```bash
ss -lntp | grep :80
```

```bash
firewall-cmd --list-all
```

Test locally:

```bash
curl http://localhost
```

If localhost works but another computer cannot connect, check the network and Firewalld configuration.

---

## 6. Website Is Not Loading by Domain Name

If the website works using the IP but not:

```text
http://ApacheIntro.com
```

check the hosts file.

### Linux Client

```bash
cat /etc/hosts
```

Example:

```text
YOUR_SERVER_IP    ApacheIntro.com
```

### Windows Client

Edit:

```text
C:\Windows\System32\drivers\etc\hosts
```

Add:

```text
YOUR_SERVER_IP    ApacheIntro.com
```

Test:

```bash
ping ApacheIntro.com
```

or:

```bash
getent hosts ApacheIntro.com
```

**Important:** Because the server uses DHCP, update the hosts file if the server receives a new IP address.

---

## 7. Wrong DocumentRoot

Check the VirtualHost configuration:

```bash
cat /etc/httpd/conf.d/httpd-vhosts.conf
```

Verify:

```apache
DocumentRoot "/var/www/html/Apache.com"
```

Check the website directory:

```bash
ls -la /var/www/html/Apache.com/
```

Make sure `index.html` exists.

Test:

```bash
curl http://localhost
```

---

## 8. Permission Problem

Check directory permissions:

```bash
ls -ld /var/www/html/Apache.com
```

Check website files:

```bash
ls -l /var/www/html/Apache.com/
```

Apache needs permission to read the website files.

Avoid using:

```bash
chmod -R 777 /var/www/html/Apache.com
```

Instead, identify and correct the specific ownership or permission problem.

---

## 9. VirtualHost Configuration Problem

If Apache is running but the wrong website is displayed, check the VirtualHost configuration:

```bash
httpd -S
```

Verify:

* `ServerName`
* `ServerAlias`
* `DocumentRoot`
* Port
* VirtualHost address

Check the configuration file:

```bash
cat /etc/httpd/conf.d/httpd-vhosts.conf
```

Then test:

```bash
httpd -t
```

Expected:

```text
Syntax OK
```

---

## 10. Check Apache Error Logs

When the website is not working, check Apache's error logs.

Default error log:

```bash
tail -f /var/log/httpd/error_log
```

If your VirtualHost uses:

```apache
ErrorLog "/var/log/httpd/apacheintro-error_log"
```

check:

```bash
tail -f /var/log/httpd/apacheintro-error_log
```

Logs can help identify:

* Configuration errors
* Permission problems
* Missing files
* Access problems

---

# Quick Troubleshooting Checklist

When the website is not working, check in this order:

```text
1. Check IP
   hostname -I

2. Check Apache
   systemctl status httpd

3. Check configuration
   httpd -t

4. Check port 80
   ss -lntp | grep :80

5. Check Firewalld
   firewall-cmd --list-all

6. Check VirtualHost
   httpd -S

7. Check DocumentRoot
   ls -la /var/www/html/Apache.com/

8. Check hosts file
   /etc/hosts

9. Check permissions
   ls -l /var/www/html/Apache.com/

10. Check Apache logs
    tail -f /var/log/httpd/error_log
```

