# DESCRIPTION
1. Create RO snapshot snapA1 on source FS
2. Run “acfsutil snap create snapA1 /source”
>
[Tue Aug 29 01:23:09][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][0]# acfsutil snap creat -h
Usage: acfsutil [-h] snap create [-w|-r] [-p parent_snap_name] <snap_name> <mountpoint>
                [-w]                      - create a writeable snapshot
                [-r]                      - create a read-only snapshot
                                            This is the default behavior
                [-p parent_snap_name]     - create a snapshot from a snapshot

[Tue Aug 29 01:29:52][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][1]# acfsutil snap creat -r snapA1 /scratch/crsusr/mount/clusterwidemount/
acfsutil snap create: Snapshot operation is complete.
[Tue Aug 29 01:32:06][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][0]# acfsutil snap creat -w -p snapA1 snapA11 /scratch/crsusr/mount/clusterwidemount/
acfsutil snap create: Snapshot operation is complete.
额外：
[Tue Aug 29 01:34:04][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][0]# acfsutil snap delete snapA11 /scratch/crsusr/mount/clusterwidemount/
acfsutil snap delete: Snapshot operation is complete.
>

3.  Run ”acfsutil snap dup create snapA1 /source | acfsutil snap dup apply -B snapB1 /target”
>
Purpose:Creates a duplicate snapshot of an existing snapshot.
*Syntax*
acfsutil snap duplicate create [-r] [-d trace_level] [-p parent_snapshot] [-i old_snapshot] new_snapshot mount_point

```
[Tue Aug 29 02:17:19][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][0]# acfsutil snap duplicate create -i snapA1 snapB1 /scratch/crsusr/mount/clusterwidemount/ | acfsutil snap dup apply -B snapB2 /scratch/crsusr/mount/mounttest/
acfsutil snap duplicate create: ACFS-05912: unable to perform 'snap duplicate' operation because ACFS replication is initialized on the file system
acfsutil snap duplicate apply: ACFS-05912: unable to perform 'snap duplicate' operation because ACFS replication is initialized on the file system
[Tue Aug 29 02:17:23][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][1]# acfsutil repl resume /scratch/crsusr/mount/clusterwidemount/
```


>

4. Run "acfsutil snap create snapB2 /target"

Variant:
1. Repeat the steps.
2. standby and primary ACFS with compress on and auto-size enabled.

# EXPECTED RESULT
1. All Commands returns with No error and hanging
2. Check alert log,no error.
3. Replication should be initialized successfully, and data should be transported to standby.

In Variant:
1. After VIP relocated, data should be transported continue 
2. replv2 supports  different OS
3. Replication should be initialized successfully with other repl user or other ssh port.

# BUG
Bug 25686174 - LINUX-12202-REPLV2: APPLY HITS ACFS-05905 AFTER MODIFY TARGET FS 
Bug 25692003 - LNX64-12202-REPLV2:DUP APPLY SHOULD GIVE ERROR MSG IF ALREADY APPLIED STREAM 

注：
1. 可能存在的文档错误
> acfsutil snap duplicate create -h displays help text and exits.
>but,
[Tue Aug 29 01:35:47][10569][root@rwsae01.us.oracle.com:/scratch/crsusr/mount/clusterwidemount][0]# acfsutil snap dupicate create -h
acfsutil: Version 12.2.0.2.0

The following commands are supported:

     accel       - Manage accelerators
     audit       - Manage Auditing
     blog        - Manage Binary Logs
     compat      - Manage compatibility levels
     compress    - Manage compression
     defrag      - Defragment a file system
     dumpstate   - Dump file system state for diagnosis
     encr        - Manage Encryption
     info        - Display file system information
     log         - Manage Kernel Logs
     meta        - Collect file system metadata
     plugin      - Manage plugins
     registry    - Add, delete, or display mount registry entries
     remote      - Manage ACFS remote files
     repl        - Manage Replication
     rmfs        - Remove a file system
     sec         - Manage security
     size        - Resize a file system
     scrub       - Check mirror consistency
     snap        - Manage Snapshots
     tag         - Manage Tags
     tune        - Modify or display tunable parameters
     version     - Display version information
     freeze      - Suspend file system updates
     thaw        - Resume file system updates

For more information, run:  acfsutil -h <command>
