---
layout: post
title: "(Untitled)"
---

```bash
$ node app.js 
node-gallery listening on localhost:3000
```

```bash
$ netstat -tanp | grep LISTEN
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN      -                   
tcp        0      0 127.0.0.1:631               0.0.0.0:*                   LISTEN      -                   
tcp        0      0 0.0.0.0:3000                0.0.0.0:*                   LISTEN      32431/node          
tcp        0      0 127.0.0.1:25                0.0.0.0:*                   LISTEN      -                   
tcp        0      0 :::22                       :::*                        LISTEN      -                   
tcp        0      0 ::1:631                     :::*                        LISTEN      -                   
tcp        0      0 ::1:25                      :::*                        LISTEN      -
```

```bash
$ sudo iptables -L -n
[sudo] password for rbianchi: 
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED 
ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22 
ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0           state NEW udp dpt:7001 
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:4241 
REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         
REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited 

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

