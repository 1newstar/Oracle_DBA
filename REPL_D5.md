# REPL_D5

1.  Create diskgroup and initialize replication on both sites.
2.  Mount ACFS file system on all nodes on primary site.
3.  Make a big new file by bonnie on the primary.

4. On master node on the primary , umount primary file system;
注：a. srvctl 可以全局控制，在本地节点stop remove其他节点
   b. umount 只能umount 本地节点

[Wed Sep 13 23:58:38][29555][root@rwsae01.us.oracle.com:~][0]# /bin/umount -v /dev/asm/p1-387 /scratch/crsusr/mount/pri/
umount.acfs: ACFS-05200: Stopping ACFS replication daemons on /scratch/crsusr/mount/pri
umount.acfs: ACFS-05203: ACFS replication daemons stopped successfully on /scratch/crsusr/mount/pri
umount: /scratch/crsusr/mount/pri (/dev/asm/p1-387) unmounted
umount: /scratch/crsusr/mount/pri/: not mounted

ASMCA 上显示 Primary Site State 从 MOUNTED(2 of 2)--->MOUNTED(1 of 2)

5. do fsck to check primary ACFS
fsck purpose:
Checks and repairs an Oracle ACFS file system.

fsck checks and repairs an existing Oracle ACFS. This command can only be run on a dismounted file system. root privileges are required to run fsck. The Oracle ACFS driver must be loaded for fsck to work.

By default, fsck only checks for and reports any errors. The -a flag must be specified to instruct fsck to repair errors in the file system. Do not interrupt fsck during the repair operation.

6. Re-mout primary
[Wed Sep 13 23:59:49][29555][root@rwsae01.us.oracle.com:~][32]# /bin/mount -v -t acfs /dev/asm/p1-387 /scratch/crsusr/mount/pri/
mount.acfs: volume: /dev/asm/p1-387, mount point: /scratch/crsusr/mount/pri
mount.acfs: options: rw
mount.acfs: verbose option specified
mount.acfs: command string: /bin/mount -i -t acfs -o ,rw /dev/asm/p1-387 /scratch/crsusr/mount/pri -v.
mount: /dev/asm/p1-387 mounted on /scratch/crsusr/mount/pri.
mount.acfs: ACFS-05200: Stopping ACFS replication daemons on /scratch/crsusr/mount/pri
mount.acfs: ACFS-05203: ACFS replication daemons stopped successfully on /scratch/crsusr/mount/pri


Variant: 
1.Mount primary  file system on only one node, and umount primary  file system when data apply is progressing. And then re-mount it.
2.The same test on standby.


知识点：
acfsutil meta -f meta.out /dev/asm/p-431 

Copies metadata from an Oracle ACFS file system into a separate output file.
