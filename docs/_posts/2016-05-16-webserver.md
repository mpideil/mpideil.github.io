---
title: A packet header exploration Post
layout: post
draft: true
---


* When sending a SYN packet to port 80, we receive a SYN-ACK response
* The SYN-ACK response has a non-null IP id
* Let’s send a SYN packet every 60 second and record the IP id of the response
<!--more-->

Example :
---------
```
[protean:/pentest]$ while [ 1 ]; do hping -p 80 www.target.com -S -c 1 ; sleep 60; done
--- www.target.com hping statistic ---
1 packets transmitted, 1 packets received, 0% packet loss
round-trip min/avg/max = 11.6/11.6/11.6 ms
HPING www.target.com (eth0 XXX.XXX.XXX.XXX): S set, 40 headers + 0 data bytes
len=46 ip=XXX.XXX.XXX.XXX ttl=116 DF id=18291 sport=80 flags=SA seq=0 win=16616 rtt=14.6 ms
[...]
```

Scapy :
=======

```
[protean:/pentest]$ scapy
Welcome to Scapy (1.2.0.2)
>>>a=sniff(filter="src host XXX.XXX.XXX.XXX and port 80",
prn=lambda x: x.sprintf("%SYSTEM.time% - id=%IP.id%"))
00:00:12.564219 - id=18291
00:01:12.214522 - id=18305
[...]
```

[[project_blog_posts max_number=10 sort=timestamp mount_point=news]]

![traffic]([[!!images/webTraffic.png]])

[My Man Jeeves by PG Wodehouse][MMJ]

[MMJ]: http://www.gutenberg.org/cache/epub/8164/pg8164.html
