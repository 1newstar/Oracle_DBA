Hit issue bug#26705155 and bug#26709002. 
For the first bug, cd to $GI_HOME/lib, copy libclntsh.so.18.1 to libclntsh.so.12.1 and copy libclntshcore.so.18.1 to libclntshcore.so.12.1. 
[Wed Aug 30 02:17:11][22177][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/lib][0]# cp libclntshcore.so.18.1 libclntshcore.so.12.1
[Wed Aug 30 02:17:29][22177][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/lib][0]# cp libclntsh.so.18.1 libclntsh.so.12.1



For the second bug, download cvuhelper122.jar and cvuhelper18.jar in the bug post and replace the original one in $GI_HOME/jlib. 
Successful with W/A


[Mon Aug 28 02:14:41][11782][root@rwsae01.us.oracle.com:~][0]# sftp frank.shi@oracle.com@bugsftp
Enter password for yuanlin.tie@oracle.com
Password:
Connected to bugsftp.
sftp> 
sftp> pwd
Remote working directory: /
sftp> cd 26709002
sftp> pwd
Remote working directory: /26709002
sftp> ls -l
-rw-r--r--   1 leowang  leowang    151295 Aug 28 01:03 GridSetupActions2017-08-27_10-39-57PM.tar.gz
-rw-r--r--   1 maboddu  maboddu    849583 Aug 28 03:30 cvuhelper122.jar
-rw-r--r--   1 maboddu  maboddu   1935654 Aug 28 03:32 cvuhelper18.jar

sftp> get cvuhelper1*
sftp> ^D

Fetching /26709002/cvuhelper122.jar to cvuhelper122.jar
/26709002/cvuhelper122.jar                 100%  830KB 829.7KB/s   00:01
Fetching /26709002/cvuhelper18.jar to cvuhelper18.jar
/26709002/cvuhelper18.jar                  100% 1890KB   1.9MB/s   00:01    
sftp> exit

[root@rwsae01.us.oracle.com:/scratch/oracle/soft][0]# cp cvuhelper1* ../CrsHome/jlib/
