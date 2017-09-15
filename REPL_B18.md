# REPL_B18
1. Setup replv2 standby with engough space(or auto-resize enabled).

此处发了一个bug：26785766
Usage: acfsutil [-h] size [[-|+]nnn[K|M|G|T|P]] [-a nnn[K|M|G|T|P]] [-x nnn[K|M|G|T|P]] [-d <device>]  <path>
                                         - Resize file system and configure auto-resize
                [-a]                     - Auto-resize increment
                [-x]                     - Auto-resize maximum
                [-d]                     - Resize only this device
1.使用方法eg:
[Mon Sep 11 21:42:32 ][root@rwsae04.us.oracle.com:~]#acfsutil size -a 1G -d /dev/asm/s-278 /mnt/s/
acfsutil size: ACFS-03642: successfully updated auto-resize settings

[Mon Sep 11 19:37:22 ][root@rwsae04.us.oracle.com:~]#acfsutil size +250G -a 200G -d /dev/asm/s-278 /mnt/s/
acfsutil size: ACFS-03171: insufficient contiguous free ASM Diskgroup space


2. Create 200G+ files in different dirs on primary ACFS.
DISK GROUP(DATA)空间不到200G，首先利ASMCA向DATA添加disk至200G

工具学习：
Bonnie++是一个硬盘和文件系统的基准性能测试工具，它通过一系列的简单测试来生成硬盘和文件系统的性能参数。主要对三个方面做基准测试：数据读、写速度，每秒可以完成的磁盘寻道次数和每秒可以完成的文件元数据操作次数。（说明： Bonnie不能支持>2G的文件，因此Russell Coker (russell@coker.com.au) 开发了Bonnie++）
http://www.coker.com.au/bonnie++/
下载到本地电脑后，上传到服务器
frshi-Mac:Users frank.shi$ cd /Users/frank.shi/Downloads/
frshi-Mac:Downloads frank.shi$ scp bonnie++_1.04.tar crsusr@rwsae01.us.oracle.com:/scratch/oracle/soft
crsusr@rwsae01.us.oracle.com's password:
bonnie++_1.04.tar                     100%  330KB 330.0KB/s   00:00
解压缩：
[Mon Sep 11 23:18:34][7856][root@rwsae01.us.oracle.com:/scratch/oracle/soft][2]# tar xvf bonnie++_1.04.tar    
[Mon Sep 11 23:19:57][7856][root@rwsae01.us.oracle.com:/scratch/oracle/soft][0]# cd bonnie++-1.04
[Mon Sep 11 23:22:58][7856][root@rwsae01.us.oracle.com:/scratch/oracle/soft/bonnie++-1.04][0]# ls -al
total 380
drwxr-xr-x 4 root   root   4096 Sep  4 21:27 .
drwxrwxr-x 8 crsusr oper   4096 Sep 11 23:19 ..
-rw-r--r-- 1 root   root   2553 Sep  4 21:27 bon_csv2html
-rw-r--r-- 1 root   root    926 Apr 20  2008 bon_csv2html.1
-rwxr-xr-x 1 root   root   2558 Nov  1  2000 bon_csv2html.in
-rw-r--r-- 1 root   root   2680 Sep  4 21:27 bon_csv2txt
-rw-r--r-- 1 root   root     24 Nov 21  1999 bon_csv2txt.1
-rwxr-xr-x 1 root   root   2685 Mar 20  2001 bon_csv2txt.in
-rw-r--r-- 1 root   root  14893 Jul  2  2009 bon_file.cpp
-rw-r--r-- 1 root   root   1532 Dec 31  2000 bon_file.h
-rw-r--r-- 1 root   root  11296 Sep  3 22:35 bon_io.cpp
-rw-r--r-- 1 root   root   1363 Jul  2  2009 bon_io.h
-rw-r--r-- 1 root   root     20 Mar 13  2001 bonnie.8
-rw-r--r-- 1 root   root   7224 Jul  3  2009 bonnie++.8
-rw-r--r-- 1 root   root  20641 Sep  3 22:32 bonnie++.cpp
-rw-r--r-- 1 root   root   1464 Sep  4 21:27 bonnie.h
-rw-r--r-- 1 root   root   1469 Sep  3 22:32 bonnie.h.in
-rw-r--r-- 1 root   root   1007 Sep  4 21:27 bonnie++.spec
-rw-r--r-- 1 root   root   1012 Jan  2  2003 bonnie++.spec.in
-rw-r--r-- 1 root   root   1576 Jul  2  2009 bon_suid.cpp
-rw-r--r-- 1 root   root   9329 Jan  8  2003 bon_time.cpp
-rw-r--r-- 1 root   root   1196 Apr 16  2001 bon_time.h
lrwxrwxrwx 1 root   root     16 Dec  9  2008 changelog.txt -> debian/changelog
-rw-r--r-- 1 root   root    189 Sep  4 21:27 conf.h
-rw-r--r-- 1 root   root    116 Jan  8  2003 conf.h.in
-rwxr-xr-x 1 root   root 136489 Sep  4 21:27 configure
-rw-r--r-- 1 root   root   2005 Sep  4 21:27 configure.in
-rw-r--r-- 1 root   root    756 Nov  3  2001 copyright.txt
-rw-r--r-- 1 root   root    461 Sep 24  2000 credits.txt
drwxr-xr-x 2 root   root   4096 Sep  4 21:27 debian
-rw-r--r-- 1 root   root   3657 Jul  2  2009 forkit.cpp
-rw-r--r-- 1 root   root    761 Oct 13  2002 forkit.h
-rwxr-xr-x 1 root   root   4541 May 26  2000 install.sh
-rw-r--r-- 1 root   root   1456 Sep  4 21:27 Makefile
-rw-r--r-- 1 root   root   1418 Oct 13  2002 Makefile.in
-rw-r--r-- 1 root   root    900 Sep  4 21:27 port.h
-rw-r--r-- 1 root   root    938 Jan  8  2003 port.h.in
-rw-r--r-- 1 root   root   9358 Jul 22  2008 readme.html
-rw-r--r-- 1 root   root   2290 Dec 31  2000 semaphore.cpp
-rw-r--r-- 1 root   root   1137 Jul  2  2009 semaphore.h
-rw-r--r-- 1 root   root   4539 Sep 28  2000 sh.common
drwxr-xr-x 2 root   root   4096 Sep  4 21:27 sun
-rw-r--r-- 1 root   root   2971 Jul 22  2008 zcav.8
-rw-r--r-- 1 root   root   8306 Sep  4 21:27 zcav.cpp
[Mon Sep 11 23:23:00][7856][root@rwsae01.us.oracle.com:/scratch/oracle/soft/bonnie++-1.04][0]# ./configure 
checking for g++... g++
checking whether the C++ compiler works... yes
checking for C++ compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C++ compiler... yes
checking whether g++ accepts -g... yes
checking how to run the C++ preprocessor... g++ -E
checking for a BSD-compatible install... /bin/install -c
checking for an ANSI C-conforming const... yes
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for ANSI C header files... yes
checking for sys/types.h... yes
checking for sys/stat.h... yes
checking for stdlib.h... yes
checking for string.h... yes
checking for memory.h... yes
checking for strings.h... yes
checking for inttypes.h... yes
checking for stdint.h... yes
checking for unistd.h... yes
checking for size_t... yes
checking vector.h usability... no
checking vector.h presence... no
checking for vector.h... no
checking vector usability... yes
checking vector presence... yes
checking for vector... yes
checking algorithm usability... yes
checking algorithm presence... yes
checking for algorithm... yes
checking algo.h usability... no
checking algo.h presence... no
checking for algo.h... no
checking algo usability... no
checking algo presence... no
checking for algo... no
configure: creating ./config.status
config.status: creating Makefile
config.status: WARNING:  'Makefile.in' seems to ignore the --datarootdir setting
config.status: creating bonnie.h
config.status: creating port.h
config.status: creating bonnie++.spec
config.status: creating bon_csv2html
config.status: creating bon_csv2txt
config.status: creating sun/pkginfo
config.status: creating conf.h
config.status: conf.h is unchanged

切换至root用户运行：
make && make install

安装完成以后会在/usr/local/sbin/目录中生成两个可执行文件，bonnie++（主测试程序）和zcav（裸盘吞吐量测试程序）；同时也会在/usr/local/bin目录下生成两个可执行文件，用于生成可读性强的测试报告，它们是bon_csv2html和bon_csv2txt。

[Mon Sep 11 23:24:16][7856][root@rwsae01.us.oracle.com:/scratch/oracle/soft/bonnie++-1.04][2]# cd /usr/local/sbin/
[Mon Sep 11 23:24:34][7856][root@rwsae01.us.oracle.com:/usr/local/sbin][0]# ls -al
total 64
drwxr-xr-x.  2 root root  4096 Sep 11 23:24 .
drwxr-xr-x. 16 root root  4096 Jun 12 12:47 ..
-rwxr-xr-x   1 root root 39704 Sep 11 23:24 bonnie++
-rwxr-xr-x   1 root root 14824 Sep 11 23:24 zcav

Bonnie++测试选项说明：

http://www.jianshu.com/p/2e048e23e9f7

成功安装bonnie++后，查看bonnie++的测试参数
[Mon Sep 11 23:27:49][3925][root@rwsae01.us.oracle.com:~][0]# bonnie++ help
usage: bonnie++ [-d scratch-dir] [-s size(MiB)[:chunk-size(b)]]
                [-n number-to-stat[:max-size[:min-size][:num-directories]]]
                [-m machine-name]
                [-r ram-size-in-MiB]
                [-x number-of-tests] [-u uid-to-use:gid-to-use] [-g gid-to-use]
                [-q] [-f] [-b] [-p processes | -y]
                [-D]

Version: 1.04

-d  起始路径

      目录名字；Bonnie将在该目录下创建测试文件并进行读写，即为需要测试的文件系统挂载的目录。bonnie不会对目录名字进行任何特殊的解释，只是按照原名字使用。

-s size-in-MB

      测试文件的大小，以MB为单位。默认是100MB大小。文件大小一定要是数倍于可用内存的大小，否则操作系统将会把文件的大部分文件cache到内存中，从而导致bonnie执行的真正的I/O次数非常少。建议设置至少为可用内存大小的2倍（另一种说法为4倍）。

-m machine-label

     给出bonnie用来生成报告的标识。如果不使用该选项，bonnie将不在报告中提供特定标识。

-html

     以html的格式输出测试结果
-m 机器名

     实际上我们可以认为是本次测试的方案名，可以随便定义。默认是本机的hostname。

-r 内存大小

  指定内存大小，这样可以通过-s参数创建r*2大小的文件，通常用于缩短测试时间，但是需要注意这样由于内存的cache可能导致测试结果的不准确

-x 测试的次数
-u 测试文件的属主和组，默认是执行bonnie++的当前用户和当前组
-g 测试文件的组，默认是执行bonnie++的当前用组
-b 在每次写文件时调用fsync()函数，对于测试邮件服务器或者数据库服务器这种通常需要同步操作的情况比较适合，而不使用该参数则比较适合测试copy文件或者编译等操作的效率。

EG：
bonnie++ -d /mnt/p/ -m rwsae01 -u root

Using uid:1000, gid:1000.
Writing with putc()...done
....


3. Setup replv2 primary.
4. During initial transfer, interrupt this transfer(kill acfsrepl_dupd process)

     acfsrepl_dupd --> ORACLE_HOME/bin/acfsrepl_dupd

[Mon Sep 11 23:36:51][7856][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][1]# ps -ef | grep acfsrepl_dupd
root      3511     1  0 Sep10 ?        00:02:15 /scratch/oracle/CrsHome/bin/acfsrepl_dupd.bin /mnt/p          
root     27797  7856  0 23:38 pts/4    00:00:00 grep --color=auto acfsrepl_dupd            

[Mon Sep 11 23:38:24][7856][root@rwsae01.us.oracle.com:/scratch/oracle/CrsHome/bin][1]# kill 3511

*****************************************************
kill了之后需要看replication那个log,看看这个process是不是重启，重启之后的replication工作是否正常。

replication log:

/scratch/oracle/crsbase/diag/crs/rwsae01/crs/trace/acfsdupd_p-278_0.trc

*****************************************************

14:58:08 qian.hu@oracle.com: ssh有问题了
14:58:13 qian.hu@oracle.com: PID变了
14:58:25 qian.hu@oracle.com:  cat /usr/tmp/oracle_ssh.out 
14:58:25 qian.hu@oracle.com: Permission denied (publickey,password).

14:59:53 qian.hu@oracle.com: 那你重新配置一下ssh
15:00:01 qian.hu@oracle.com: 让replication正常之后
15:00:07 qian.hu@oracle.com: 重新做一下这个case。
15:00:31 qian.hu@oracle.com: 注意primary 和standby 做好只mount的正确的node上面
