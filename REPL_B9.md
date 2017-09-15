# Description
1. Create  read-write snapshotp on primary and  snapshots on standby
2.  Init replv2 using snapshots.
Use "acfsutil repl init standby -u repluser snapshots@mounts "
Use " acfsutil repl init primary -s standby_connect_string  -C -m snapshots@mounts  snapshotp@mountp 

> Usage: acfsutil [-h] repl init {primary | standby}       - which site
                -u <user>                 - standby only, replication user
                -s <standby_conn_string>  - primary only, standby connect
                                            string
                {-C|-M|-i <interval>}     - primary only, constant-mode,
                                            manual-mode replication
                                            or replication interval
                [-m [<snapshot>@]<mountpoint>]
                                          - primary only, standby mount point or
                                            snapshot
                [-d {0..6}]               - set trace level for debugging
                [-z {on|off}]             - primary only, data transfer
                                            compression
                [<tagname> ...]           - primary only, tags to be replicated
                [-o sshCmdPath=<ssh command path>]
                                          - primary only, path to ssh command
                [-o sshCipher=<cipher>]   - primary only, ssh cipher
                [-o sshStrictKey={y|n}]   - primary only, require ssh host key
                                            checking
                [<snapshot>@]<mountpoint> - mount point or snapshot for
                                            replication

[Tue Aug 29 04:29:34][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][1]# acfsutil snap create snapA1 -w /scratch/crsusr/mount/pri/
acfsutil snap create: Snapshot operation is complete.

[Tue Aug 29 04:32:57][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][1]# acfsutil snap create snapB1 -w /scratch/crsusr/mount/sta/
acfsutil snap create: Snapshot operation is complete.

[Tue Aug 29 04:33:20][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][0]# acfsutil repl init primary -C -s crsusr@rwsae04 -m snapB1@/scratch/crsusr/mount/sta/ snapA1@/scratch/crsusr/mount/pri/

[Tue Aug 29 04:36:16 ][root@rwsae04.us.oracle.com:~]#acfsutil repl init standby -u crsusr snapB1@/scratch/crsusr/mount/sta/

[Tue Aug 29 04:46:44][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][1]# acfsutil repl info -c snapA1@/scratch/crsusr/mount/pri/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        snapA1@/scratch/crsusr/mount/pri
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        snapB1@/scratch/crsusr/mount/sta
Replication interval:                0 days, 0 hours, 0 minutes, 0 seconds
Sending primary as of:               Tue Aug 29 04:45:22 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Tue Aug 29 04:45:23 2017
Last send completed at:              Tue Aug 29 04:45:24 2017
Elapsed time for last send:          0 days, 0 hours, 0 minutes, 1 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

3. Try some replv2 commands,such as pause/resume/bg and so on.

[Tue Aug 29 05:02:13][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][1]# acfsutil repl pause snapA1@/scratch/crsusr/mount/pri/
[Tue Aug 29 05:03:41][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][0]# acfsutil repl info -c snapA1@/scratch/crsusr/mount/sta/
acfsutil repl info: ACFS-03051: Snapshot snapA1 does not exist.
[Tue Aug 29 05:03:50][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount][1]# acfsutil repl info -c snapA1@/scratch/crsusr/mount/pri/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        snapA1@/scratch/crsusr/mount/pri
Primary status:                      Paused
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        snapB1@/scratch/crsusr/mount/sta
Replication interval:                0 days, 0 hours, 0 minutes, 0 seconds
Sending primary as of:               Tue Aug 29 04:45:22 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Tue Aug 29 04:45:23 2017
Last send completed at:              Tue Aug 29 04:45:24 2017
Elapsed time for last send:          0 days, 0 hours, 0 minutes, 1 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

4. Try snapshot opertions with the snapshots and snapshotp

[Tue Aug 29 05:06:17][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/pri][0]# truncate -s 1G beau.img
[Tue Aug 29 05:09:27][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/pri][0]# truncate -s 1G shdb.ora

[Tue Aug 29 05:07:06][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/pri][0]# acfsutil repl sync apply -h
Usage: acfsutil [-h] repl sync [apply]                   - return when all is applied
                [<snapshot>@]<mountpoint> - mount point or snapshot on primary
[Tue Aug 29 05:07:49][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/pri][0]# acfsutil repl sync apply snapA1@/scratch/crsusr/mount/pri/

[Tue Aug 29 05:07:49 ][root@rwsae04.us.oracle.com:/scratch/crsusr/mount/pri]#ls -l
total 72
-rw-r--r-- 1 root root 1073741824 Aug 29 05:06 beau.img
drwx------ 2 root root      65536 Aug 29 04:24 lost+found
-rw-r--r-- 1 root root 1073741824 Aug 29  2017 shdb.ora

Variant: 
1. Update replv2 related  parameters via "repl update"

[Tue Aug 29 05:20:34][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/pri][1]# acfsutil repl update -s crsusr@rwsae03 -d 3 -i 1m snapA1@/scratch/crsusr/mount/pri/

[Tue Aug 29 05:30:00 ][root@rwsae04.us.oracle.com:~]#acfsutil repl update -u racusr -d 3 snapB1@/scratch/crsusr/mount/sta/

2. standby and primary ACFS with compress on and auto-size enabled.

