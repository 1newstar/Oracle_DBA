[Tue Aug 29 19:22:13 ][root@rwsae04.us.oracle.com:/dev]#swapon -s
Filename                                Type            Size    Used    Priority
/LOCAL_SWAP/swapfile1                   file    2047996 0       -1
/LOCAL_SWAP/swapfile2                   file    2047996 0       -2
/LOCAL_SWAP/swapfile4                   file    2047996 0       -3
/LOCAL_SWAP/swapfile3                   file    2047996 0       -4
/dev/sda3                               partition       4194300 0       -5
[Tue Aug 29 20:03:28 ][root@rwsae04.us.oracle.com:/dev]#dd if=/dev/zero of=/LOCAL_SWAP/swapfile5 bs=2048 count=2097152
2097152+0 records in
2097152+0 records out
4294967296 bytes (4.3 GB) copied, 20.292 s, 212 MB/s
[Tue Aug 29 20:03:51 ][root@rwsae04.us.oracle.com:/dev]#mkswap /LOCAL_SWAP/swapfile5
Setting up swapspace version 1, size = 4194300 KiB
no label, UUID=bbeeaa5e-c2b5-48c7-9adf-f781eaae391a
[Tue Aug 29 20:03:55 ][root@rwsae04.us.oracle.com:/dev]#swapon /LOCAL_SWAP/swapfile5
swapon: /LOCAL_SWAP/swapfile5: insecure permissions 0644, 0600 suggested.
[Tue Aug 29 20:04:02 ][root@rwsae04.us.oracle.com:/dev]#swapon -a
[Tue Aug 29 20:04:09 ][root@rwsae04.us.oracle.com:/dev]#swapon -s
Filename                                Type            Size    Used    Priority
/LOCAL_SWAP/swapfile1                   file    2047996 0       -1
/LOCAL_SWAP/swapfile2                   file    2047996 0       -2
/LOCAL_SWAP/swapfile4                   file    2047996 0       -3
/LOCAL_SWAP/swapfile3                   file    2047996 0       -4
/dev/sda3                               partition       4194300 0       -5
/LOCAL_SWAP/swapfile5                   file    4194300 0       -6
[Tue Aug 29 20:04:12 ][root@rwsae04.us.oracle.com:/dev]#
