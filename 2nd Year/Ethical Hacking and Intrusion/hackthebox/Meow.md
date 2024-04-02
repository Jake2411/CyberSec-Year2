# 😸 Meow



This was a very easy box to complete and literally took ~5mins to complete (these notes probs took more time to write than to do the machine).

## 1. Enumerate the machine by running nmap:

```
sudo nmap -p- 10.129.1.17
```

This scan shows telnet is open

![[Pasted image 20240301120839.png]]

## Accessing Telnet

Telnet can be accessed using the following command:

```
telnet 10.129.1.17
```

![[Pasted image 20240301121042.png]]

I googled "default telnet credentials" and found that by default no password is set.

I tried `root` as the login and it just let me in. A quick `ls` shows that the flag is stored in the current folder:

![[Pasted image 20240301121200.png]]