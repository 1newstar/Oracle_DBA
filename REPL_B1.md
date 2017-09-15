# Case REPL_B1
1. 
terminate的时候必须先在primary node执行，再在standby node执行
acfsutil repl terminate primary /scratch/crsusr/mount/clusterwidemount/
//acfsutil repl terminate: ACFS-05060: waiting for ACFS replication to terminate
acfsutil repl terminate standby /scratch/crsusr/mount/clusterwidemount/
2. 
Wenzhe Shi
15:52 另外 Variant
15:53 VIP 是指 rwsae01-v等吗

qian.hu@oracle.com
15:53 对
15:53 或者rwsae01-v对应的IP address
15:53 你可以用nslookup rwsae01-v看到这个地址

Wenzhe Shi
15:53 这一步的作用是？VIP和 rwsae01 有什么区别
15:53 好的

qian.hu@oracle.com
15:53 如果你用VIP。
15:54 VIP可以漂移的
15:54 01重启了，就是挂了。replication就断了
15:54 如果用VIP.01 重启 01-vip会飘到其他存活节点
15:55 但是存活节点的ssh OK，replication还是可以继续工作的

3. ssh登陆VIP
[Tue Aug 22 01:17:07][15021][crsusr@rwsae01.us.oracle.com:~][0]$ nslookup rwsae04-v
Server:         10.210.255.250
Address:        10.210.255.250#53

Name:   rwsae04-v.us.oracle.com
Address: 10.208.145.223

[Tue Aug 22 01:17:17][15021][crsusr@rwsae01.us.oracle.com:~][0]$ ssh crsusr@10.208.145.223 hostname 
The authenticity of host '10.208.145.223 (10.208.145.223)' can't be established.
ECDSA key fingerprint is 69:ae:11:c6:d5:ee:a8:ce:07:c2:56:78:4e:a6:4a:08.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.208.145.223' (ECDSA) to the list of known hosts.
rwsae04.us.oracle.com
[Tue Aug 22 01:17:33][15021][crsusr@rwsae01.us.oracle.com:~][0]$ ssh crsusr@10.208.145.223 hostname 
rwsae04.us.oracle.com
[Tue Aug 22 01:17:36][15021][crsusr@rwsae01.us.oracle.com:~][0]$ hostname
rwsae01.us.oracle.com

注解：
[Tue Aug 22 01:30:14][21967][root@rwsae01.us.oracle.com:~][0]# nslookup rwsae04-v
Server:         10.210.255.250
Address:        10.210.255.250#53
[Tue Aug 22 01:31:01][21967][root@rwsae01.us.oracle.com:~][127]# ssh crsusr@10.208.145.223 date
Tue Aug 22 01:31:11 PDT 2017

在01节点上连接04节点的VIP，并执行一条命令后返回，如正常执行，则连接成功
ssh crsusr@10.208.145.223 如不加命令，则连接到VIP地址

# repl VIP SSH连接问题
针对 acfsutil repl init: ACFS-05914: user root on node rwsae01.us.oracle.com unable to log in via 'ssh' to the standby node 10.208.145.220 as user crsusr
执行：
ssh repluser@standby 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
18:31 Run as root on primary node


#  acfsutil 要指定 -m
Usage: acfsutil [-h] repl init {primary | standby}       - which site
                -u <user>                 - standby only, replication user
                -s <standby_conn_string>  - primary only, standby connect
                                            string
                {-C|-M|-i <interval>}     - primary only, constant-mode,
                                            manual-mode replication
                                            or replication interval
                [-m [<snapshot>@]<mountpoint>]
                                          - primary only, standby mount                            point or snapshot  《-----
                                            需要-m 指定standby ACFS---》

格式：                                            
acfsutil repl init primary -C -s crsusr@10.208.145.223 -m standbyomuntpath /scratch/crsusr/mount/clusterwidemount/
Eg：
[Tue Aug 22 02:24:59][10943][root@rwsae01.us.oracle.com:~][1]# acfsutil repl init primary -C -m /scratch/crsusr/mount/mounttest/ -s crsusr@10.208.145.220 /scratch/crsusr/mount/clusterwidemount/
