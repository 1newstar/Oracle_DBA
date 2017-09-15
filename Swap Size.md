# Swap Size

[Sun Sep 10 18:25:12][27111][root@rwsae01.us.oracle.com:~][0]# swapon -s
Filename                                Type            Size    Used    Priority
/LOCAL_SWAP/swapfile2                   file    2047996 0       -1
/LOCAL_SWAP/swapfile1                   file    2047996 0       -2
/LOCAL_SWAP/swapfile3                   file    2047996 0       -4
/LOCAL_SWAP/swapfile4                   file    2047996 0       -3
/dev/sda3                               partition       4194300 0       -5
[Sun Sep 10 18:25:20][27111][root@rwsae01.us.oracle.com:~][0]# dd if=/dev/zero of=/LOCAL_SWAP/swapfile5 bs=1024k count=5000
5000+0 records in
5000+0 records out
5242880000 bytes (5.2 GB) copied, 23.2075 s, 226 MB/s
[Sun Sep 10 18:27:49][27111][root@rwsae01.us.oracle.com:~][0]# mkswap /LOCAL_SWAP/swapfile5
Setting up swapspace version 1, size = 5119996 KiB
no label, UUID=cfa8db48-6e72-4341-a761-d13683083274
[Sun Sep 10 18:28:17][27111][root@rwsae01.us.oracle.com:~][0]# swapon /LOCAL_SWAP/swapfile5
swapon: /LOCAL_SWAP/swapfile5: insecure permissions 0644, 0600 suggested.
[Sun Sep 10 18:31:11][27111][root@rwsae01.us.oracle.com:~][255]# swapon -a
[Sun Sep 10 18:32:31][27111][root@rwsae01.us.oracle.com:~][0]# swapon -s
Filename                                Type            Size    Used    Priority
/LOCAL_SWAP/swapfile2                   file    2047996 0       -1
/LOCAL_SWAP/swapfile1                   file    2047996 0       -2
/LOCAL_SWAP/swapfile3                   file    2047996 0       -4
/LOCAL_SWAP/swapfile4                   file    2047996 0       -3
/dev/sda3                               partition       4194300 0       -5
/LOCAL_SWAP/swapfile5                   file    5119996 0       -6
