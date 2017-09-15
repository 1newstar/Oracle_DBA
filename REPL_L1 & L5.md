# REPL_L1 & L5

# REPL_L1
1. Within a single cluster, create two acfs file systems
2. Setup replication between these two acfs file systems.
3. Put  ORACLE_HOME on acfs fs1, install RAC software on fs1, and create  RAC DB
4. Keep RAC DB long running, and run workload on it.

Variant :
1. Mount ACFS file system on all nodes, but some nodes mount RO and others RW
2. Setup replication on both site.
3. Long running replication.
4. On replicated ACFS fs on primary site, 
Use a program, which will create a file and truncate its size to any given number, including null.
Create all kinds of files,such as sparse file,loop devide,link,zip,IMG, socket, char device and block device which FileSytem supported.
5. Run snapshot  workload



# REPL_L5
注：google检索 Archive logs/flashback logs、 RMAN
1. Put Archive logs/flashback logs on primary ACFS.
理解：Archive logs是DB备份的log

2. Make DB backup by RMAN and restore DB backup by RMAN with the backup file. Put Rman files on primary ACFS.


3. Put data pump files on primary ACFS，by expdp/impdp

expdp/impdp 工具
