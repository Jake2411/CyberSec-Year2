# Superset

After spending two hours on this room, googling various things that came from the nmap scans, Metasploit searches etc, I started to search the internet and by chance came across this [page]( https://www.hackthebox.com/blog/9-critical-cybersecurity-trends-for-2024) that says this about the Superset room:

> "Test your web application skills as you attempt to exploit a vulnerability in Apache Superset."

Googling "Apache superset vulnerability" showed results that included version numbers and this made me think to the `about` section on the target machine's website:

![[Pasted image 20240228211056.png]]

After putting these two things together I have googled "apache superset 1.4.0 exploit" and come across this [page](https://security.snyk.io/package/pip/apache-superset/1.4.0) from Snyk security.

---

## Getting Nowhere
**& Getting frustrated :/**

After more extensive searching I have found nothing to help me move forwards.

So far, I know that the machine has two open ports:
- 22 for ssh - OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
- 8088 for http - Werkzeug httpd 1.0.1 (Python 3.8.10)

The website can be reached at `http://10.129.194.194:8088`
- Running Apache Superset version 1.4.0

The machine is not vulnerable to the Metasploit: `exploit(multi/http/werkzeug_debug_rce`

![[Pasted image 20240228215728.png]]

The machine **is** vulnerable to a slowloris exploit but I am unsure what this is useful for / what it is doing:

![[Pasted image 20240228220209.png]]

---

https://help.hackthebox.com/en/articles/7986207-may-updates-new-exclusive-training-machines