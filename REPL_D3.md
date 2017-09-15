# REPL_D3

1.  Create diskgroup and initialize replication on both sites.
2.  Mount ACFS file system on all nodes on primary site.
3.  On non-master node make a big new file, and make sure that the file is transporting.

做这个case，不能直接连resae01然后断掉 公网网卡，因为repl是通过公网传输。
需要从其他节点（比如03）连私网 网卡(10.208.160.42)进入 01，这样的话断 公网网卡，不会被踢出

4.  On master node, failure public or private nic by ‘/sbin/ifdown’;

網路介面控制器（英语：network interface controller，NIC），又稱网络接口控制器，网络适配器（network adapter），网卡（network interface card），或區域網路接收器（LAN adapter），是一块被设计用来允许计算机在计算机网络上进行通讯的计算机硬件。由于其拥有MAC地址，因此属于OSI模型的第1层。它使得用户可以通过电缆或无线相互连接。每一个网卡都有一个被称为MAC地址的独一无二的48位序列号，它被写在卡上的一块ROM中。在网络上的每一个计算机都必须拥有一个独一无二的MAC地址。没有任何两块被生产出来的网卡拥有同样的地址。这是因为电气电子工程师协会（IEEE）负责为网络接口控制器销售商分配唯一的MAC地址。
网卡以前是作为扩展卡插到计算机总线上的，但是由于其价格低廉而且以太网标准普遍存在，大部分新的计算机都在主板上集成了网络接口。这些主板或是在主板芯片中集成了以太网的功能，或是使用一块通过PCI (或者更新的PCI-Express总线)连接到主板上的廉价网卡。除非需要多接口或者使用其它种类的网络，否则不再需要一块独立的网卡。甚至更新的主板可能含有内置的双网络（以太网）接口。

> ## 通用方法
1. 
[Tue Sep 12 04:15:35][19140][root@rwsae01.us.oracle.com:/sbin][0]# ifconfig -a
eno1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.160.42  netmask 255.255.252.0  broadcast 10.208.163.255
        inet6 fe80::221:28ff:fe3d:efcb  prefixlen 64  scopeid 0x20<link>
        ether 00:21:28:3d:ef:cb  txqueuelen 1000  (Ethernet)
        RX packets 22870086  bytes 15526691741 (14.4 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19817567  bytes 16972926919 (15.8 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eno1:1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 169.254.16.163  netmask 255.255.240.0  broadcast 169.254.31.255
        ether 00:21:28:3d:ef:cb  txqueuelen 1000  (Ethernet)

eno2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.152.46  netmask 255.255.252.0  broadcast 10.208.155.255
        inet6 fe80::221:28ff:fe3d:efcc  prefixlen 64  scopeid 0x20<link>
        ether 00:21:28:3d:ef:cc  txqueuelen 1000  (Ethernet)
        RX packets 20690902  bytes 15926526226 (14.8 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 20205547  bytes 17322577156 (16.1 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eno2:2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 169.254.8.232  netmask 255.255.240.0  broadcast 169.254.15.255
        ether 00:21:28:3d:ef:cc  txqueuelen 1000  (Ethernet)

eno3: flags=4098<BROADCAST,MULTICAST>  mtu 1500
        ether 00:21:28:3d:ef:cd  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.144.42  netmask 255.255.248.0  broadcast 10.208.151.255
        inet6 2606:b400:400:784a:221:28ff:fe3d:efca  prefixlen 64  scopeid 0x0<global>
        inet6 fe80::221:28ff:fe3d:efca  prefixlen 64  scopeid 0x20<link>
        ether 00:21:28:3d:ef:ca  txqueuelen 1000  (Ethernet)
        RX packets 21545857  bytes 18963028942 (17.6 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 35747923  bytes 47253000068 (44.0 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth0:1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.147.172  netmask 255.255.248.0  broadcast 10.208.151.255
        ether 00:21:28:3d:ef:ca  txqueuelen 1000  (Ethernet)

eth0:2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.145.220  netmask 255.255.248.0  broadcast 10.208.151.255
        ether 00:21:28:3d:ef:ca  txqueuelen 1000  (Ethernet)

eth0:4: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.147.130  netmask 255.255.248.0  broadcast 10.208.151.255
        ether 00:21:28:3d:ef:ca  txqueuelen 1000  (Ethernet)

eth0:5: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.208.147.129  netmask 255.255.248.0  broadcast 10.208.151.255
        ether 00:21:28:3d:ef:ca  txqueuelen 1000  (Ethernet)

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 0  (Local Loopback)
        RX packets 7839163  bytes 7295737752 (6.7 GiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 7839163  bytes 7295737752 (6.7 GiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
2. 
[Tue Sep 12 04:20:59][19140][root@rwsae01.us.oracle.com:~][0]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN 
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether 00:21:28:3d:ef:ca brd ff:ff:ff:ff:ff:ff
    inet 10.208.144.42/21 brd 10.208.151.255 scope global eth0
    inet 10.208.147.172/21 brd 10.208.151.255 scope global secondary eth0:1
    inet 10.208.145.220/21 brd 10.208.151.255 scope global secondary eth0:2
    inet 10.208.147.130/21 brd 10.208.151.255 scope global secondary eth0:4
    inet 10.208.147.129/21 brd 10.208.151.255 scope global secondary eth0:5
    inet6 2606:b400:400:784a:221:28ff:fe3d:efca/64 scope global dynamic 
       valid_lft 2591837sec preferred_lft 604637sec
    inet6 fe80::221:28ff:fe3d:efca/64 scope link 
       valid_lft forever preferred_lft forever
3: eno1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether 00:21:28:3d:ef:cb brd ff:ff:ff:ff:ff:ff
    inet 10.208.160.42/22 brd 10.208.163.255 scope global eno1
    inet 169.254.16.163/20 brd 169.254.31.255 scope global eno1:1
    inet6 fe80::221:28ff:fe3d:efcb/64 scope link 
       valid_lft forever preferred_lft forever
4: eno2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP qlen 1000
    link/ether 00:21:28:3d:ef:cc brd ff:ff:ff:ff:ff:ff
    inet 10.208.152.46/22 brd 10.208.155.255 scope global eno2
    inet 169.254.8.232/20 brd 169.254.15.255 scope global eno2:2
    inet6 fe80::221:28ff:fe3d:efcc/64 scope link 
       valid_lft forever preferred_lft forever
5: eno3: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 00:21:28:3d:ef:cd brd ff:ff:ff:ff:ff:ff
    
3. get just the Ip addresses use this small shell script:
[Tue Sep 12 04:21:38][19140][root@rwsae01.us.oracle.com:~][0]# ip addr | awk '/inet / {sub(/\/.*/, "", $2); print $2}'
127.0.0.1
10.208.144.42
10.208.147.172
10.208.145.220
10.208.147.130
10.208.147.129
10.208.160.42
169.254.16.163
10.208.152.46
169.254.8.232

> ## 针对此问题的方法
When you install GI,you will choose the private nic(like eth1/eth2...) and public nic(eth0).If you forget the nic names,you can check the private nic by "oifcfg getif"

[Tue Sep 12 18:42:49][29884][crsusr@rwsae01.us.oracle.com:~][0]$ oifcfg getif
eno1  10.208.160.0  global  cluster_interconnect,asm  <<---private
eno2  10.208.152.0  global  cluster_interconnect,asm  <<---private
eth0  10.208.144.0  global  public                    <<---public


/sbin/ifdown nic-name  will do failure the nic.

1.repl init 前在 not master node 上建立大文件
[Wed Sep 13 03:03:55 ][root@rwsae04.us.oracle.com:/mnt/p]#bonnie++ -d /mnt/p/ -m rwsae04 -u crsusr
Using uid:65000, gid:8501.
Writing with putc()...

[Wed Sep 13 03:13:49 ][root@rwsae04.us.oracle.com:/mnt/p]#ls -l
total 44875868
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0000 <-----1G
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0001
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0002
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0003
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0004
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0005
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0006
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0007
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0008
-rw-r--r-- 1 crsusr oper 1073741824 Sep 13 03:14 Bonnie.27165.0009
(共40G)
.....
传输 in progress
[Wed Sep 13 03:25:10][1873][root@rwsae01.us.oracle.com:~][0]# acfsutil repl info -c /mnt/p/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        /mnt/p
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        /mnt/s
Replication interval:                0 days, 0 hours, 0 minutes, 10 seconds
Sending primary as of:               Wed Sep 13 03:22:55 2017
Status:                              Sending initial copy
Retries made:                        0
Last send started at:                Wed Sep 13 03:22:55 2017
Last send completed at:              In progress
Next send starts at:                 On send completion
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

send completed 共8min+
[Wed Sep 13 03:30:50][1873][root@rwsae01.us.oracle.com:~][0]# acfsutil repl info -c /mnt/p/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        /mnt/p
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        /mnt/s
Replication interval:                0 days, 0 hours, 0 minutes, 10 seconds
Sending primary as of:               Wed Sep 13 03:22:55 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Wed Sep 13 03:22:55 2017
Last send completed at:              Wed Sep 13 03:31:21 2017
Elapsed time for last send:          0 days, 0 hours, 8 minutes, 26 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

查公网 私网 网卡 name
[Wed Sep 13 03:30:00][1873][root@rwsae01.us.oracle.com:~][0]# oifcfg getif
eno1  10.208.160.0  global  cluster_interconnect,asm <---- ifdown this one
eno2  10.208.152.0  global  cluster_interconnect,asm
eth0  10.208.144.0  global  public

ifconfig eno1 ip地址：10.208.160.42
[Wed Sep 13 03:28:54][12314][crsusr@rwsae03.us.oracle.com:~][0]$ ssh crsusr@10.208.160.42
Last login: Wed Sep 13 03:20:28 2017

[Wed Sep 13 03:31:56][13575][crsusr@rwsae01.us.oracle.com:/sbin][1]$ su -
Password: 
Last login: Wed Sep 13 03:17:31 PDT 2017 on pts/2
[Wed Sep 13 03:32:03][15507][root@rwsae01.us.oracle.com:~][0]# /sbin/ifdown eno1
此处命令一直在执行 未返回
很久后返回
[Wed Sep 13 03:32:03][15507][root@rwsae01.us.oracle.com:~][0]# /sbin/ifdown eno1
Write failed: Broken pipe

在另外节点上 ping：
[Wed Sep 13 03:39:29 ][root@rwsae04.us.oracle.com:/mnt/p]#ping 10.208.160.42
PING 10.208.160.42 (10.208.160.42) 56(84) bytes of data.
From 10.208.160.45 icmp_seq=1 Destination Host Unreachable
From 10.208.160.45 icmp_seq=2 Destination Host Unreachable
From 10.208.160.45 icmp_seq=3 Destination Host Unreachable
From 10.208.160.45 icmp_seq=4 Destination Host Unreachable
From 10.208.160.45 icmp_seq=5 Destination Host Unreachable
....
已经 shutdown

查看repl log 
2017-09-13 03:48:42.335 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=1547, delayTime=10
2017-09-13 03:48:42.339 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
2017-09-13 03:48:52.339 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=1557, delayTime=10
2017-09-13 03:48:52.343 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
2017-09-13 03:49:02.343 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=1567, delayTime=10
2017-09-13 03:49:02.347 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
2017-09-13 03:49:12.348 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=1577, delayTime=10
2017-09-13 03:49:12.351 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10

发现repl 完好

回到01，ssh未断开
[Wed Sep 13 03:49:06][1873][root@rwsae01.us.oracle.com:~][0]# /sbin/ifup eno1
RTNETLINK answers: File exists
[Wed Sep 13 03:39:56 ][root@rwsae04.us.oracle.com:/mnt/p]#ping 10.208.160.42
PING 10.208.160.42 (10.208.160.42) 56(84) bytes of data.
64 bytes from 10.208.160.42: icmp_seq=1 ttl=64 time=0.212 ms
64 bytes from 10.208.160.42: icmp_seq=2 ttl=64 time=0.211 ms
64 bytes from 10.208.160.42: icmp_seq=3 ttl=64 time=0.206 ms
64 bytes from 10.208.160.42: icmp_seq=4 ttl=64 time=0.195 ms
^C

猜想断另一个private nic 情况一样

ifdown  public nic
[Wed Sep 13 03:53:42][28286][root@rwsae01.us.oracle.com:~][0]# /sbin/ifdown eth0

log：
9, delayTime=10
2017-09-13 03:54:24.167 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
>   18:55:47 Socket error: Operation timed out
>   18:55:47 Disconnected from server
>   18:55:47 Connection failed, retry after 3 seconds…
>   18:55:50 Connecting to rwsae01.us.oracle.com port 22
>   18:55:50 Command: ssh -p 22 crsusr@rwsae01.us.oracle.com
>   18:55:50 Connect to rwsae01.us.oracle.com:22, with timeout 15.000000
>   18:55:50 Create socket with IPv4 address family
>   18:56:05 Socket connect timed out
>   18:56:05 Operation timed out
>   18:56:05 Disconnected from server
>   18:56:05 Connection failed, retry after 3 seconds…
>   18:56:08 Connecting to rwsae01.us.oracle.com port 22
>   18:56:08 Command: ssh -p 22 crsusr@rwsae01.us.oracle.com
>   18:56:08 Connect to rwsae01.us.oracle.com:22, with timeout 15.000000
>   18:56:08 Create socket with IPv4 address family
>   18:56:23 Socket connect timed out
>   18:56:23 Operation timed out
>   18:56:23 Disconnected from server
>   18:56:23 Connection failed, retry after 3 seconds…
>   18:56:26 Connecting to rwsae01.us.oracle.com port 22
>   18:56:26 Command: ssh -p 22 crsusr@rwsae01.us.oracle.com

repl log 中断，rwsae01 连接断开
在03上 ifup
[Wed Sep 13 03:57:23][28286][root@rwsae01.us.oracle.com:~][0]# /sbin/ifup eth0
RTNETLINK answers: File exists

[Wed Sep 13 03:58:01][30877][root@rwsae01.us.oracle.com:~][0]# acfsutil repl info -c /mnt/p/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        /mnt/p
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        /mnt/s
Replication interval:                0 days, 0 hours, 0 minutes, 10 seconds
Sending primary as of:               Wed Sep 13 03:22:55 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Wed Sep 13 03:22:55 2017
Last send completed at:              Wed Sep 13 03:31:21 2017
Elapsed time for last send:          0 days, 0 hours, 8 minutes, 26 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

repl完好 ,log正常


[Wed Sep 13 03:57:46][30577][crsusr@rwsae01.us.oracle.com:~][0]$ tail -f /scratch/oracle/crsbase/diag/crs/rwsae01/crs/trace/acfsdupd_p-278_0.trc
2017-09-13 04:00:04.300 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=2229, delayTime=10
2017-09-13 04:00:04.303 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
2017-09-13 04:00:14.303 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=2239, delayTime=10
2017-09-13 04:00:14.307 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
2017-09-13 04:00:24.307 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: waited=10, interval=10, elapsed=2249, delayTime=10
2017-09-13 04:00:24.311 :  CLONED:2810015808: [9813]usrcAwaitNextTransfer: delayTime=10, remRecTime=10, failures=0, interval=10
2017-09-13 04:00:34.311 :  
.....



Variant: 
When only one node on primary site, failure public nic on primary, and then resume ; To check if data replication can be continue or not.
When only one private nic on primary site, failure private nic on primary, and then resume ; To check if data replication can be continue or not.
