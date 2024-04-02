
```shell
sudo nmap -sV -A -O *ip*
```

```shell
sudo nmap --script vuln *ip*
```

![[Pasted image 20240305142258.png]]
The result of both nmap scans is in the above picture

Port 8080 and 8081 are open and connected to HTTP so I went on the website that was linked to that

![[Pasted image 20240305142504.png]]
This was the website and it shows that the version of apache spark is 3.0.3 so i did a bit of googling to see if i could find an exploit based around this version

![[Pasted image 20240305142704.png]]
I found this website that talked about a Remote Code Execution vulnerability

So i searched for it on metasploit
![[Pasted image 20240305142840.png]]
Two results pop back but only one is an RCE by the looks of it so I chose that

I did the options, which were RHOSTS and LHOST and then ran the exploit, hoping for a reverse shell
![[Pasted image 20240305143121.png]]
This error came back

I did a bit of googling around and saw a few things that talked about changing ACL settings in the website. This turned out to be wrong. After asking a fellow student (Maxwell), I was told that the issue was the payload was wrong. So i searched for more payloads in the exploit with `show payloads`

![[Pasted image 20240305143244.png]]
A lot of payloads came back
The one Maxwell used was this one
![[Pasted image 20240305143335.png]]
And it worked! I had my reverse shell with root access and after some looking around I had my flag!
![[Pasted image 20240305143556.png]]
