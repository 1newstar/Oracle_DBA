# REPL_B7
1.  Initialize replication on both sites

2. To stop replication background processes
Run “acfsutil repl bg stop /mountp” on primary
**ACFS 均是mount on multi-nodes**

(1) 当bg info查看，Current State: OFFLINE on rwsae01，再在01上bg stop，没有效用
[Thu Aug 24 01:10:28][23273][root@rwsae01.us.oracle.com:~][0]# acfsutil repl bg stop /scratch/crsusr/mount/clusterwidemount/
[Thu Aug 24 01:10:47][23273][root@rwsae01.us.oracle.com:~][0]# acfsutil repl bg info /scratch/crsusr/mount/clusterwidemount/
Resource:      ora.repl.dupd.data.frank.acfs
Target State:  OFFLINE           , OFFLINE           , ONLINE           
Current State: OFFLINE on rwsae01, OFFLINE on rwsae03, ONLINE on rwsae04 
> bg：ONLINE on rwsae04，bg 只能online在一个node上面，因此在01上bg start，没有效用
[Thu Aug 24 01:10:57][23273][root@rwsae01.us.oracle.com:~][0]# acfsutil repl bg start /scratch/crsusr/mount/clusterwidemount/
[Thu Aug 24 01:11:21][23273][root@rwsae01.us.oracle.com:~][0]# acfsutil repl bg info /scratch/crsusr/mount/clusterwidemount/
Resource:      ora.repl.dupd.data.frank.acfs
Target State:  OFFLINE           , OFFLINE           , ONLINE           
Current State: OFFLINE on rwsae01, OFFLINE on rwsae03, ONLINE on rwsae04

(2) bg stop操作导致bgfailover到其他可用的node
> ACFS mount on multi-nodes
[Thu Aug 24 01:14:46][26260][root@rwsae03.us.oracle.com:~][0]# acfsutil repl bg stop /scratch/crsusr/mount/clusterwidemount/
[Thu Aug 24 01:15:08][26260][root@rwsae03.us.oracle.com:~][0]# acfsutil repl bg info /scratch/crsusr/mount/clusterwidemount/
Resource:      ora.repl.dupd.data.frank.acfs
Target State:  OFFLINE           , OFFLINE           , ONLINE           
Current State: OFFLINE on rwsae01, OFFLINE on rwsae03, ONLINE on rwsae04 

[Thu Aug 24 01:34:15 ][root@rwsae04.us.oracle.com:~]#acfsutil repl bg stop /scratch/crsusr/mount/clusterwidemount/
[Thu Aug 24 01:34:34 ][root@rwsae04.us.oracle.com:~]#acfsutil repl bg info /scratch/crsusr/mount/clusterwidemount/
Resource:      ora.repl.dupd.data.frank.acfs
Target State:  OFFLINE           , ONLINE           , OFFLINE           
Current State: OFFLINE on rwsae01, ONLINE on rwsae03, OFFLINE on rwsae04

 > 如果primary ACFS只mount在一个node上面，只要bg 开始是online在这个node，做了stop，bg 就会offline on this node


3. To start replication backgroup processes
Run “acfsutil repl bg start /mountp” on primary
4. Run “acfsutil repl bg info /mountp” on both sites
To get information about replication background processes.
 
Variant:
1. Run "acfsutil snap dup" operations after replv2 setup.

注： creat snap 前 必须要 terminate replication

 snap duplicate create    [-r] [-p parentsnap] [-i oldsnapname] [-d {0..6}] <newsnapname> <mountpoint>
 snap duplicate create    [-r]              - restart of data stream
 snap duplicate create    [-p parentsnap]   - parent snap for base site
 snap duplicate create    [-i oldsnapname]  - old snapshot name
 snap duplicate create    [-d {0..6}]       - set trace level for debugging
 snap duplicate create    <newsnapname>     - new snapshot name
 snap duplicate create    <mountpoint>      - mount point for base site

 snap duplicate apply     [-b|-B <snap_name>] [-d {0..6}] [<snap_name>] <mountpoint>
 snap duplicate apply     -b                       - maintain backup snapshot
 snap duplicate apply     -B snap_name             - maintain named backup snapshot
 snap duplicate apply     [-d {0..6}]              - set trace level for debugging
 snap duplicate apply     [<snap_name>]            - target snapshot
 snap duplicate apply     <mountpoint>             - mount point for target site

> 期望报错:
> 6. "snap dup create/apply" should be rejected on replication configuration.
> 7. 'snap duplicate create' requires read-only snapshot
正确：
[Mon Aug 28 01:35:27][11782][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][1]# acfsutil snap duplicate create -r -i newsnap1 -d 3 wrankopt /scratch/crsusr/mount/clusterwidemount/

acfsutil snap duplicate create: ACFS-05912: unable to perform 'snap duplicate' operation because ACFS replication is initialized on the file system

> 可能有 bug，下个label测一下
**apply操作有错，hang住了但没有报错，可能是bug**
错误写法：
[Mon Aug 28 01:14:57][11782][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][1]# acfsutil snap duplicate apply -b -d 3 frankopt /scratch/crsusr/mount/clusterwidemount/


2. standby and primary ACFS with compress on and auto-size enabled.(尚未解决)
http://docs.oracle.com/database/122/OSTMG/acfs-commands-compression.htm
> $ /sbin/acfsutil compress copy -v -f my_file1 my_file2 /my_target_directory ?

Compressed files are associated with a compression unit and the compression algorithm operates on this unit. For database files, the size of the unit is made equal to the database block size for maximum efficiency. For non-database files, the unit size is currently 32 kilobytes. When copying data files to an Oracle ACFS file system that has been enabled for compression, use the acfsutil compress copy command to ensure that the proper compression unit size is maintained for correct functionality. lzo is the default compression algorithm and the only compression algorithm currently supported.

The acfsutil compress command sets and resets the compression state of a file system with acfsutil compress on and acfsutil compress off. To display the compression state and effectiveness of the compression operation, use the acfsutil compress info file command. The acfsutil info fs and acfsutil info file commands have been enhanced to report on Oracle ACFS compression status.

Compressed files consume less disk space than non-compressed files. However, for applications using the file, the size reported is equal to the uncompressed file size, not the smaller compressed size. Some utilities, such as ls -l, report the uncompressed size of the file. Utilities such as du, acfsutil compress info, and acfsutil info file, report the actual disk allocation of the compressed file.

Usage: acfsutil [-h] compress copy  [-v] [-f] { -c <size> <source> | [-n] {-r <source> | <source> [...]} } <target>
                               - Copy a file or files onto an ACFS
                                 file system that has compression enabled
                 [-v]          - Verbose
                 [-f]          - Overwrite existing files
                 {-c <size> <source> }  - Copy a single file and use a
                                          compression unit size for
                                          the copy
                                 <size> : compression unit size in
                                          bytes as nnn[K]
                 [-n]          - copies will not be compressed
                 {-r <source>} - recursively copy files and directories
                                 starting from a single directory
                 {<source> [...]} - files to copy
                 <target>      - directory to copy into


[Mon Aug 28 20:07:20][28621][root@rwsae01.us.oracle.com:~][1]# acfsutil compress off /scratch/crsusr/mount/clusterwidemount/
[Mon Aug 28 20:09:39][28621][root@rwsae01.us.oracle.com:~][0]# acfsutil compress on -a lzo /scratch/crsusr/mount/clusterwidemount/

[Mon Aug 28 20:10:05][28621][root@rwsae01.us.oracle.com:~][0]# acfsutil compress info /scratch/crsusr/mount/clusterwidemount/sun.ora 
The file /scratch/crsusr/mount/clusterwidemount/sun.ora is not compressed.


[Mon Aug 28 03:02:30][11782][root@rwsae01.us.oracle.com:~][0]# acfsutil info fs -o mountpoints
/scratch/crsusr/mount/clusterwidemount
/scratch/crsusr/mount/mounttest

[Mon Aug 28 02:55:17][11782][root@rwsae01.us.oracle.com:~][0]# acfsutil info fs -o iscompression 
0
0


Resizes an Oracle ACFS file system.
Syntax:
acfsutil size [ [+|-]n[K|M|G|T|P] ] [ -a n[K|M|G|T] ] [ -x n[K|M|G|T] ] [-d volume_device] mount_point

> 注：At least one of the + or —, —a, or —x options must be specified.

Eg:
[Mon Aug 28 23:05:10][20046][root@rwsae01.us.oracle.com:~][1]# acfsutil size -a 2G /scratch/crsusr/mount/clusterwidemount/
acfsutil size: ACFS-03642: successfully updated auto-resize settings

> 'snap duplicate create' requires read-only snapshot
