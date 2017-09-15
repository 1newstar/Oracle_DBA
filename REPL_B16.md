# REPL_B16

1. Init replication on primary and standby
2. Enable Auditing on primary.
报错
[Mon Sep 04 01:14:19][10952][user1@rwsae01.us.oracle.com:/root][1]$ acfsutil audit enable -m /scratch/crsusr/mount/pri/ -s encr
acfsutil audit enable: ACFS-10594: Internal error : OCR  27 : Failed to get audit related information from OCR Key.
[Mon Sep 04 01:14:27][10952][user1@rwsae01.us.oracle.com:/root][1]$ acfsutil audit enable -m /scratch/crsusr/mount/pri/ -s sec
acfsutil audit enable: ACFS-10594: Internal error : OCR  27 : Failed to get audit related information from OCR Key.

3. Try all audit command(archive/read/purge/enable/disable/info) on primary











注：
acfsutil audit init -M audit_manager_group -A auditor_group
> Only a system administrator can run this command.

acfsutil audit info [-m mount_point ]
> Only an audit manager can run this command.

**新建用户命令**
[Mon Sep 04 00:05:36][8207][root@rwsae01.us.oracle.com:~][0]# useradd -G group1 -d /scratch/tt -s /bin/bash -u 1383 user1

useradd: warning: the home directory already exists.
Not copying any file from skel directory into it.
[Mon Sep 04 00:05:48][8207][root@rwsae01.us.oracle.com:~][0]# 
[Mon Sep 04 00:05:48][8207][root@rwsae01.us.oracle.com:~][0]# cd /scratch/

注：
/etc/group  (在里面添加的用户和组是副组)
/etc/passwd 
