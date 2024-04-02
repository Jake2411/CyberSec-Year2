![[Pasted image 20240306164307.png]]
Nmap scan results show that the ports open are 22 and 80. With 80 being a webshell so I followed PwnKit up to the privilege escalation

# User Own

Follow the [[PwnKit]] notes

# System Own

The pwnkit priv escalation didn't work so I had to google around until I found some escalation exploit called Enlightenment. I tried this, as it had the same name as the box
And it worked and I was in as a root user!
![[Pasted image 20240306164610.png]]
![[Pasted image 20240306164630.png]]
![[Pasted image 20240306164646.png]]
