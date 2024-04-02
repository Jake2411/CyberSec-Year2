# 🧠 Brainfuck

#htb-insane

---

# Initial Nmap Scan

```
sudo nmap -p- 10.129.228.97
```

```
sudo nmap -p 22,25,110,143,443 -sCV 10.129.228.97
```

![[Pasted image 20240302105007.png]]

This showed 5 open ports:
- 22 - OpenSSH  7.2p2 Ubuntu  4Ubuntu2.1
- 25 - SMTP Postfix 
- 110 - Dovecot Pop3
- 143 - Dovecot Imapd
- 443 - HTTPS - ssl/http nginx 1.10.0 ubuntu

## HTTPS Nginx Website

\# Visiting `https://10.129.228.97:443`

![[Pasted image 20240302104526.png]]

### TLS Certificate

The certificate can be seen by clicking the padlock in Firefox. The common name and some alt names (SANs):

- `brainfuck.htb` - common name also shown in Nmap scan
- `www.brainfuck.htb`
- `sup3rs3cr3t.brainfuck.htb`

![[Pasted image 20240302105247.png]]

These domains can be added to `etc/hosts` to resolve the target IP address to a fully qualified domain name (FQDN).

\# Add domains to `/etc/hosts` using `sudo nano` and adding:

```
10.129.228.97 brainfuck.htb www.brainfuck.htb sup3rs3cr3t.brainfuck.htb
```

Now I can access a WordPress site using `brainfuck.htb`:

![[Pasted image 20240302111134.png]]

The page shows a post made by user: `admin` so I have a potential login username.

---

# 🔗 Useful Links

- Guide for `wpscan`: [wpscan intro](https://blog.sucuri.net/2023/12/wpscan-intro-how-to-scan-for-wordpress-vulnerabilities.html)
