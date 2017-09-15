# REPL_B15
1. Init replication on primary and standby

2. Try to Init Sec on primary .

3. Try to Init Encr on primary.

4. Enable encryption on both sides.  

5. Encrypt a lot of files.
> lots of files就是创建几百上万或者更多文件就行。

6. Encryption rekey.

[Sun Sep 03 22:06:54][16874][root@rwsae01.us.oracle.com:~][0]# acfsutil encr on -m /scratch/crsusr/mount/pri/
acfsutil encr on: ACFS-10411: encryption is already enabled on the file system
13:07 [Sun Sep 03 22:05:41 ][root@rwsae04.us.oracle.com:~]#acfsutil encr on -m /scratch/crsusr/mount/sta/
acfsutil encr on: ACFS-10423: This encryption operation is not allowed on a standby file system.
> 在Primary 上 运行，enabled on the file system，是不是就意味着  enable on both sides？
> 答案： 是的。

7. Disable encryption on both sides.

[Sun Sep 03 22:17:06][23184][crsusr@rwsae01.us.oracle.com:~/mount/pri][1]$ acfsutil encr off -m /scratch/crsusr/mount/pri/
acfsutil encr off: ACFS-10410: encryption is already disabled on the file system
[Sun Sep 03 22:17:07][23184][crsusr@rwsae01.us.oracle.com:~/mount/pri][1]$ acfsutil encr off -m /scratch/crsusr/mount/sta/
acfsutil encr off: ACFS-10423: This encryption operation is not allowed on a standby file system.



Do above steps on primary while replication running.
> Do above steps on primary while replication running.就是保证repl  bg running就行了
