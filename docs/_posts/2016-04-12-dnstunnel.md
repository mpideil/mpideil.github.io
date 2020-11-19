---
title: Dns tunnel
layout: post
draft: true
---
![Python](/assets/images/python.jpeg)

It's been since 2 month or more I can't have decent internet access. 3G is is nice ... not so nice.

The ninja inside me wants more and I decided to grant him that pleasure, using Wifi open access points.
<!--more-->

When we connect to an open wifi access, the DHCP give us an IP a gateway and a DNS server.
Before you login through an authentication page, the captive portal waits for an HTTP request to be made to rewrite its content. But for that it let DNS request to pass through.
We can use that flow to make a data tunnel

## Explications

Here we go. The drawns will come later...

When you ask to join hello.toto.com your current DNS server (the captive portal in the common case) will requestest rootservers to get the .com autoritative servers. Then It asks for toto.com to the .com autohoritative servers. toto.com is our server so we can modify the DNS server that will answer to the waptive portal DNS server.

So if we request the resolution of a.toto.com and if the NS of toto.com is our server, we will receive the request and then be able to answer what we want to.

In the zone file we have
```
dt	NS	hello.toto.com.
hello	A	IP_FAKE_NS
```

After propagation of record (~3h) we can set the software layer

We will use dns2tcp wich is client and server.

In the file /etc/dns2tcpd.conf :
```
listen = 192.168.0.100
port = 53
# If you change this value, also change the USER variable in /etc/default/dns2tcpd
user = nobody
chroot = /tmp
domain = dt.xx.xx
resources = ssh:127.0.0.1:22
# , smtp:127.0.0.1:25
```
Then the command:
```
dns2tcpd -F -d 1 -f /etc/dns2tcpd.conf
```
(debug mode )


On the client the file /etc/dns2tcpd.conf :
```
#listen = 127.0.0.1
#port = 53
# If you change this value, also change the USER variable in /etc/default/dns2tcpd
#user = nobody
#chroot = /tmp
domain = dt.temp.eu
resources = ssh
#:127.0.0.1:22 , smtp:127.0.0.1:25
local_port = 2222
debug_level = 1
```

And then :
```
Toto@Titi:~$ dns2tcpc -z dt.temp.eu 8.8.8.8
Available connection(s) : 
	ssh
```

```
Toto@Titi:~$ dns2tcpc -c -r ssh -l 8888 -z dt.temp.eu 8.8.8.8
Listening on port : 8888
```

And so on, you've understood, we just have to connect to local port ...
