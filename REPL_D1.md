# REPL_D1

renamedg does not update resources, nor does renamedg update any file references within the database. Because of this behavior, the original disk group resource is not automatically deleted after the completion of phase two. 
The status of the old disk group resource can be checked with the Oracle Clusterware Control (CRSCTL) crsctl stat res -t command and then manually deleted with the Server Control Utility (SRVCTL) srvctl remove diskgroup command.



1.  Initialize replication on both sites
参照REPL_B2_B3_B4
[Tue Sep 12 02:18:49 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#acfsutil repl init standby -u crsusr /mnt/s/
[Tue Sep 12 02:28:53][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][1]# acfsutil repl init primary -C -s crsusr@rwsae04 -m /mnt/s/ /mnt/p/

2.  Mount ACFS fs on multi nodes on the primary site.
[Tue Sep 12 02:34:41][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# crsctl stat res ora.data.p.acfs
NAME=ora.data.p.acfs
TYPE=ora.acfs.type
TARGET=ONLINE           , OFFLINE
STATE=ONLINE on rwsae01, OFFLINE
[Tue Sep 12 02:36:02][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# ./srvctl start filesystem -volume p -diskgroup data -node rwsae04
[Tue Sep 12 02:36:52][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# crsctl stat res ora.data.p.acfs
NAME=ora.data.p.acfs
TYPE=ora.acfs.type
TARGET=ONLINE           , ONLINE
STATE=ONLINE on rwsae01, ONLINE on rwsae04

3.  Replication-related resource are online on only one node (replication master node). 
[Tue Sep 12 02:36:54][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# acfsutil repl info -c /mnt/p/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        /mnt/p
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        /mnt/s
Replication interval:                0 days, 0 hours, 0 minutes, 0 seconds
Sending primary as of:               Tue Sep 12 02:36:59 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Tue Sep 12 02:36:59 2017
Last send completed at:              Tue Sep 12 02:37:00 2017
Elapsed time for last send:          0 days, 0 hours, 0 minutes, 1 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

问：
qian ,REPL_D1
2.  Mount ACFS fs on multi nodes on the primary site.
3.  Replication-related resource are online on only one node (replication master node). 
3个节点，mount ACFS fs on 3 nodes on the primary site. 
怎么理解 Replication-related resource are online on only one node ？

答：
你看repl info -c里面primary node是哪个节点，所有resource应该就online在这个节点
acfs要 srvctl stop 另外两个节点才能保证Online on only master node，但是replication一定只在一个node上面

4.  Make a big file on non-master node.
[Tue Sep 12 03:05:48 ][crsusr@rwsae04.us.oracle.com:/mnt/p]$bonnie++ -d /mnt/p/ -m rwsae01 -u crsusr
Using uid:65000, gid:8501.
Writing with putc()...

5.  When transport is progressing, kill ASM pmon on master node. And cause ASM reconfigure. 
[Tue Sep 12 03:07:12][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# ps -ef |grep ASM pmon
grep: pmon: No such file or directory
[Tue Sep 12 03:07:32][7946][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][2]# ps -ef |grep pmon
crsusr    4873     1  0 Sep10 ?        00:00:06 mdb_pmon_-MGMTDB
crsusr    5323     1  0 Sep10 ?        00:00:06 asm_pmon_+ASM1
root     10856  7946  0 03:08 pts/6    00:00:00 grep --color=auto pmon
crsusr   15041     1  0 Sep10 ?        00:00:07 apx_pmon_+APX1
racusr   17193     1  0 Sep10 ?        00:00:10 ora_pmon_orcl_2
[Tue Sep 12 03:11:16][12526][crsusr@rwsae01.us.oracle.com:/scratch/oracle/crsbase/diag/asm/+asm/+ASM1/trace][0]$ tail -f alert_+ASM1.log

6.  When new replication master node was elected, kill new master ASM instance,cause nested ASM instance reconfiguration.


Variant: 
1.When data is transporting, dirty-reset/reboot  master node.
2.kill APX pmon process.




注：

qian ,REPL_D1
2.  Mount ACFS fs on multi nodes on the primary site.
3.  Replication-related resource are online on only one node (replication master node). 


3个节点，mount ACFS fs on 3 nodes on the primary site. 
怎么理解 Replication-related resource are online on only one node ？

crsctl stat res
(1) NAME=ora.data.p.acfs
TYPE=ora.acfs.type
TARGET=ONLINE           , ONLINE, ONLINE
STATE=ONLINE on rwsae01, rwsae03, rwsae04

(2) NAME=ora.repl.dupd.data.p.acfs
TYPE=ora.acfsrepldupd.type
TARGET=ONLINE
STATE=ONLINE on rwsae01

qian.hu@oracle.com
15:36 你看repl info -c里面primary node是哪个节点，所有resource应该就online在这个节点

Wenzhe Shi
15:36 mount multi nodes, res-acfs 肯定 online 3 nodes
15:39 岂不是要 srvctl stop 另外两个节点才能保证Online on only  master node,

qian.hu@oracle.com
15:39 acfs是，但是replication一定是在一个node上面

Wenzhe Shi
15:40 get
