## User and System Own

```shell
sudo nmap -sV -A -O *ip*
```

```shell
sudo nmap --script vuln *ip*
```

![[Pasted image 20240304145554.png]]
Result of both the nmaps. Http is open so we search the ip on the firefox

![[Pasted image 20240304145701.png]]
Result of the website
Needed to look at the source code to see if anything was put in there

![[Pasted image 20240304145740.png]]
And look at that. We have a lead. Nibbleblog was a directory

![[Pasted image 20240304145812.png]]
This is the nibbleblog page

![[Pasted image 20240304145853.png]]
This is the admin page. Which we could find through trial and error of typing other directories into the link

![[Pasted image 20240304150836.png]]
Login for the admin was the default username and password found through google:
Admin:nibbles
![[Pasted image 20240304155924.png]]
The images has an exploit where a php file can be uploaded instead of an image
![[Pasted image 20240304155821.png]]
In the folders there is a php reverse shell. We uploaded this to the website with changes to the IP and port to match our needs. Then we went to a link that was in the exploit and connected to the reverse shell using the `multi/handler` in metasploit
![[Pasted image 20240304155946.png]]
Then use polkit privilege escalation to give myself admin
![[Pasted image 20240304160020.png]]
And from there I can access the flags!

