# REPL_D6


1. Create 1022 snapshots on standby
(把完整脚本copy到路径下)

usage:
acfs_create_snap.pl  -m /path -n snap_num

[Thu Sep 14 02:28:16 ][root@rwsae04.us.oracle.com:/scratch/crsusr/yuyao/cdcasm-workload/cdcasm_workload/src/ACFS_function_unit]#./acfs_create_snap.pl -m /scratch/crsusr/mount/sta/ -n 1022
.....
INFO: Geting snapshot using "/sbin/acfsutil  snap info w /scratch/crsusr/mount/sta/"
INFO: Using command: /sbin/acfsutil  snap info w /scratch/crsusr/mount/sta/ 

INFO: Creating snapshot using "/sbin/acfsutil  snap create -w -p parent_snapshot_244_13801 parent_snapshot_1_13801 /scratch/crsusr/mount/sta/"
INFO: Using command: /sbin/acfsutil  snap create -w -p parent_snapshot_244_13801 parent_snapshot_1_13801 /scratch/crsusr/mount/sta/ 

SUCCESS: Create snapshot "parent_snapshot_1_13801" successfully !

INFO: 
############################ Create "1022" snapshots on "/scratch/crsusr/mount/sta/" (End)################################



2. Initialize standby and primary
[Thu Sep 14 02:32:10 ][root@rwsae04.us.oracle.com:/scratch/crsusr/yuyao/cdcasm-workload/cdcasm_workload/src/ACFS_function_unit]#acfsutil repl init standby -u crsusr /scratch/crsusr/mount/sta/

[Thu Sep 14 02:33:40][27252][root@rwsae01.us.oracle.com:~][1]# acfsutil repl init primary -C -s crsusr@rwsae04 -m /scratch/crsusr/mount/sta/ /scratch/crsusr/mount/pri/

[Thu Sep 14 02:34:37][27252][root@rwsae01.us.oracle.com:~][0]# acfsutil repl info -c /scratch/crsusr/mount/pri/
Site:                                Primary
Primary hostname:                    rwsae01.us.oracle.com
Primary path:                        /scratch/crsusr/mount/pri
Primary status:                      Running
Background Resources:                Active

Standby connect string:              crsusr@rwsae04
Standby path:                        /scratch/crsusr/mount/sta
Replication interval:                0 days, 0 hours, 0 minutes, 0 seconds
Sending primary as of:               Thu Sep 14 02:34:31 2017
Status:                              Send Completed
Retries made:                        0
Last send started at:                Thu Sep 14 02:34:32 2017
Last send completed at:              Thu Sep 14 02:34:32 2017
Elapsed time for last send:          0 days, 0 hours, 0 minutes, 0 seconds
Next send starts at:                 now
Replicated tags:                     
Data transfer compression:           Off
ssh strict host key checking:        On
Debug log level:                     3

Variant:
[Thu Sep 14 02:35:43][27252][root@rwsae01.us.oracle.com:~][0]# acfsutil repl terminate primary /scratch/crsusr/mount/pri/
acfsutil repl terminate: ACFS-05060: waiting for ACFS replication to terminate
[Thu Sep 14 02:36:35][27252][root@rwsae01.us.oracle.com:~][1]# acfsutil repl terminate standby /scratch/crsusr/mount/sta/

1. Create 1023 snapshots on standby

[Thu Sep 14 02:36:26 ][root@rwsae04.us.oracle.com:/scratch/crsusr/yuyao/cdcasm-workload/cdcasm_workload/src/ACFS_function_unit]#./acfs_create_snap.pl -m /scratch/crsusr/mount/sta/ -n 1

[Thu Sep 14 02:39:46][27252][root@rwsae01.us.oracle.com:~][0]# acfsutil snap info /scratch/crsusr/mount/sta/

    .......
    snapshot name:               SDBACKUP_1505381672_REPL_0_1505381671_0
    snapshot location:           /scratch/crsusr/mount/sta/.ACFS/snaps/SDBACKUP_1505381672_REPL_0_1505381671_0
    RO snapshot or RW snapshot:  RO(REPL,DUP)
    parent name:                 /scratch/crsusr/mount/sta/
    snapshot creation time:      Thu Sep 14 02:34:32 2017
    file entry table allocation: 8781824   (   8.38 MB )
    storage added to snapshot:   8781824   (   8.38 MB )

    number of snapshots:  1023
    snapshot space usage: 8279326720  (   7.71 GB )


2. Initialize standby and primary


[Thu Sep 14 02:37:47 ][root@rwsae04.us.oracle.com:/scratch/crsusr/yuyao/cdcasm-workload/cdcasm_workload/src/ACFS_function_unit]#acfsutil repl init standby -u crsusr /scratch/crsusr/mount/sta/

[Thu Sep 14 02:38:01 ][root@rwsae04.us.oracle.com:/scratch/crsusr/yuyao/cdcasm-workload/cdcasm_workload/src/ACFS_function_unit]#acfsutil repl init standby -u crsusr /scratch/crsusr/mount/sta/

1023 snapshots are allowed，
./acfs_create_snap.pl 创建更多snapshot时，acfsutil snap info 维持1023不再增加
此时initilize还可以成功。

Note:
 >>COMPATIBLE.ADVM =12.1.0.2 or higher,1023 snapshots are allowed , otherwise 63 snapshots are allowed.

Variant:
re-test SEC_ENCR with Replication runs out of snapshots.
1. Init sec/enc/aud operations on primary.
2. Create 1022 snapshots on standby
3. Initialize replication;
4. Do sec/enc/aud operations on primary.
