# REPL_D2

1.  Initialize replication on both sites;
2.  Mount ACFS file system on multi nodes on the primary site.
3.  Replication-related resources should be online on only one node( replication master node). 
4.  Make a big file on non-master node.

5.  When transport is progressing, kill cssd.bin/ stop CRS stack  on master node, and cause CSS reconfigure.

[Tue Sep 12 04:04:41][19140][root@rwsae01.us.oracle.com:~][0]# ps -ef | grep CRS
root     19604 19140  0 04:05 pts/5    00:00:00 grep --color=auto CRS

6.  When new replication master node was elected, kill cssd.bin process on new master node, cause nested reconfiguration for css.

Variant: 
When data is transporting, crash master node; and when new master was elected, crash new master node quickly.
