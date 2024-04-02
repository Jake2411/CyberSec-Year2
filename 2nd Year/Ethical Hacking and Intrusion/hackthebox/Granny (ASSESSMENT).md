## User and System Own

```shell
sudo nmap -sV -A -O *ip*
```

```shell
sudo nmap --script vuln *ip*
```

Do the nmap scans first
![[Pasted image 20240304120733.png]]

Only port that is open is port 80 so we can access the website
![[Pasted image 20240304120536.png]]
This is what comes up

But through the screenshot above, on port 80 is Microsoft IIS HTTPD 6.0
Googling this exploit comes up with a rapid7 link which shows this exploit
![[Pasted image 20240304120839.png]]

I searched for this on metasploit
![[Pasted image 20240304121257.png]]

I ran the exploit and it opened a session
![[Pasted image 20240304121455.png]]

Trying to get into the admin and the Lakis user causes an access error
![[Pasted image 20240304121655.png]]

We need to escalate our privileges once again! Metasploit has another great feature to suggest us the ways for privilege escalation. The exploits are suggested based on the architecture and the platform the user has the shell open in, along with the available exploits in meterpreter. I got a list of exploits that the target machine is probably vulnerable to!

run post/multi/recon/local_exploit_suggester

![[Pasted image 20240304122552.png]]
Now background the meterpreter session
After googling around the best exploit would be ms14_070. So we search for that on metasploit
![[Pasted image 20240304122829.png]]

But before any of this will work to get the flag I need to use migrate to enable privilege escalation exploit on the machine. It ups our privileges a little bit. To do this I need to use the `ps` command to show the processes and then use migrate on the command which has NETWORK/AUTHORITY on it. For me this was 2628
![[Pasted image 20240304124115.png]]
As you can see this puts me into the WINDOWS/SYSTEM32 area

After this we can run the exploit again and this time it puts me into the separate meterpreter session with admin
From here I can cd and cat around until i find all the flags and complete the room