#  REPL_B13

1. Init replication on primary and standby

## Create an Oracle ADVM volume in a mounted disk group with the ASMCMD volcreate command.
[Thu Sep 07 02:59:21][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]$ ps -ef |grep  pmon
crsusr    4233     1  0 Sep06 ?        00:00:04 asm_pmon_+ASM1
crsusr    4662     1  0 Sep06 ?        00:00:04 apx_pmon_+APX1
crsusr   13952 10872  0 02:59 pts/2    00:00:00 grep --color=auto pmon
racusr   23651     1  0 Sep06 ?        00:00:05 ora_pmon_STDBSI
[Thu Sep 07 02:56:03][10872][crsusr@rwsae01.us.oracle.com:~][0]$ cd /scratch/oracle/CrsHome/bin/
[Thu Sep 07 02:57:58][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]$ export ORACLE_HOME=/scratch/oracle/CrsHome
[Thu Sep 07 02:58:42][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]$ export ORACLE_SID=+ASM1
[Thu Sep 07 02:59:31][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]$ ./asmcmd
ASMCMD> 
ASMCMD> volcreate -G data -s 13G volume4
ASMCMD> volcreate -G data -s 13G volume5
ASMCMD> volcreate -G data -s 13G volume6
ASMCMD> exit

[Thu Sep 07 03:07:40][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]$ mkfs.acfs -v /dev/asm/volume4-54 -n volume4 -a /dev/asm/volume5-54 13G
> -a 后跟的vol 是 accel
[Thu Sep 07 03:07:48][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]$ mkfs.acfs -v /dev/asm/volume6-54 -n volume6
[Thu Sep 07 03:09:04][10872][crsusr@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][1]$ su -
Password: 

[Thu Sep 07 03:10:18][20931][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# ./srvctl add filesystem -d /dev/asm/volume4-54 -path /mnt/p/

[Thu Sep 07 03:13:49][20931][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][1]# ./srvctl start filesystem -d /dev/asm/volume4-54

[Thu Sep 07 03:18:54][20931][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][1]# crsctl stat res ora.data.volume4.acfs
NAME=ora.data.volume4.acfs
TYPE=ora.acfs_cluster.type
TARGET=ONLINE
STATE=ONLINE on rwsae01


切换到rwsae04：
[Thu Sep 07 03:19:42 ][root@rwsae04.us.oracle.com:~]#cd /scratch/oracle/CrsHome/bin/
[Thu Sep 07 03:19:51 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#./srvctl add filesystem -volume volume6 -diskgroup data -node rwsae01,rwsae03,rwsae04 -path /mnt/s/
[Thu Sep 07 03:20:00 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#./srvctl start filesystem -volume volume6 -diskgroup data -node rwsae04
[Thu Sep 07 03:20:26 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#
[Thu Sep 07 03:20:30 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#crsctl stat res ora.data.volume6.acfs
NAME=ora.data.volume6.acfs
TYPE=ora.acfs_cluster.type
TARGET=ONLINE
STATE=ONLINE on rwsae04

init replicate：
04：standby
[Thu Sep 07 03:21:31 ][root@rwsae04.us.oracle.com:~]#acfsutil repl init standby -u crsusr /mnt/s/
[Thu Sep 07 03:21:43 ][root@rwsae04.us.oracle.com:~]#
[Thu Sep 07 03:21:48 ][root@rwsae04.us.oracle.com:~]#acfsutil repl info -c /mnt/s/
Site:                                Standby
Standby path:                        /mnt/s
Replication interval:                0 days, 0 hours, 0 minutes, 0 seconds
Last sync time with primary:         N/A
Receiving primary as of:             N/A
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

01：primary
[Thu Sep 07 03:19:10][20931][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# acfsutil repl init primary -C -s crsusr@rwsae04 -m /mnt/s/ /mnt/p/
[Thu Sep 07 03:24:13][20931][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][0]# acfsutil repl info  -c /mnt/p/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        /mnt/p
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        /mnt/s
Replication interval:                0 days, 0 hours, 0 minutes, 0 seconds
Sending primary as of:               Thu Sep 07 03:24:07 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Thu Sep 07 03:24:08 2017
Last send completed at:              Thu Sep 07 03:24:08 2017
Elapsed time for last send:          0 days, 0 hours, 0 minutes, 0 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3


************************************************************************
这个报错奇怪？asmca建没问题，手工建start有这个问题。
<[Thu Sep 07 03:26:29 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#./srvctl start filesystem -volume volume6 -diskgroup data -node rwsae03
PRCA-1138 : failed to start one or more file system resources:
CRS-2552: There are no available instances of resource 'ora.data.volume6.acfs' to start.>
************************************************************************


2. Umount primary ACFS and standby

[Thu Sep 07 03:31:31][20931][root@rwsae01.us.oracle.com:~][0]# /bin/umount -t acfs /dev/asm/volume4-54 /mnt/p/
umount: /mnt/p/: not mounted

[Thu Sep 07 03:26:54 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#/bin/umount -t acfs /dev/asm/volume6-54 /mnt/s/
umount: /mnt/s/: not mounted

3. Replace a primary volume's accelerator volume with a new accelerator volume.

> acfsutil accel replace
Purpose:Replaces an existing accelerator volume with a new one.
This command allows an administrator to replace an existing accelerator volume with a new accelerator volume. The command is useful if the current accelerator volume is full and cannot be grown, or to migrate an accelerator to a faster volume.
<u The file system must be unmounted on all nodes to use this command.-u> The new accelerator must be at least as large as the existing accelerator. If new accelerator volume is larger than the existing accelerator volume, then the administrator should run acfsutil resize -d new_accel_volume after the file system is remounted following the acfsutil accel replace operation to enable Oracle ACFS to utilize the entire space of the new accelerator volume.

注：第一遍测case遇到的概念问题：
[Sun Sep 03 01:08:20][31962][root@rwsae01.us.oracle.com:~][1]# acfsutil accel replace -a /dev/asm/trank-54 /dev/asm/frank-54 
acfsutil accel replace: ACFS-03256: primary volume has no accelerator volume to replace
>> 原因
ASMCA建立volume不会指定 accel，需手动建立
见 上述步骤 or creates an oracle acfs file system.md

(1). mkfs -t acfs /dev/asm/*
(2). mount -t
(3). srvctl add filesystem
(4). srvctl start filsytem
(5). replace
[Thu Sep 07 02:12:49][12695][root@rwsae01.us.oracle.com:~][0]# acfsutil accel replace -a /dev/asm/volume5-54 /dev/asm/volume4-54 
primary volume:         /dev/asm/volume4-54
old accelerator volume: /dev/asm/volume5-54
Updating CRS dependencies for resource 'ora.data.volume4.acfs'.
acfsutil accel replace: ACFS-03287: unable to find old accelerator volume resource name
acfsutil accel replace: ACFS-03287: unable to find old accelerator volume resource name
CRS dependencies were updated successfully.
acfsutil accel replace: ACFS-03266: The specified new accelerator volume is already the accelerator volume that corresponds to the specified primary volume.


4. Re-mount standby firstly,then umount primary ACFS
为什么要 re-mount？ The file system must be unmounted on all nodes to use ‘replace’ command.

[Thu Sep 07 03:40:45][20931][root@rwsae01.us.oracle.com:~][0]# /bin/mount -t acfs /dev/asm/volume4-54 /mnt/p/
[Thu Sep 07 03:42:16 ][root@rwsae04.us.oracle.com:/scratch/oracle/CrsHome/bin]#/bin/mount -t acfs /dev/asm/volume6-54 /mnt/s/



Variant:
1. standby and primary ACFS with compress on and auto-size enabled.

[Thu Sep 07 04:07:16][20931][root@rwsae01.us.oracle.com:/sbin][1]# acfsutil compress info /mnt/p/frnakopt.img 
Compression Unit size: 32768
Disk storage used:   (  32.00 KB )
Disk storage saved:  ( 1023.97 MB )
Storage used is 1% of what the uncompressed file would use.
File is not scheduled for asynchronous compression.
[Thu Sep 07 04:10:15][20931][root@rwsae01.us.oracle.com:/sbin][0]# 

