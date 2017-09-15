# udev是什么？
udev是Linux 2.6内核里的一个功能，它替代了原来的devfs，成为当前Linux默认的设备管理工具。udev以守护进程的形式运行，通过侦听内核发出来的uevent来管理/dev目录下的设备文件。
在Linux中，所有的设备都是以设备文件的形式存在的。在早期的Linux版本中，“/dev”目录包含了所有可能出现的设备的设备文件，所以，Linux用户很难在这些大量的设备文件中找到匹配条件的设备文件。现在，udev只为那些连接到Linux操作系统的设备产生设备文件，并且udev能通过定义一个udev规则（rule）来产生匹配设备属性的设备文件，这些设备属性可以是内核设备名称、总线路径、厂商名称、型号、序列号或者磁盘大小等等。当设备添加/删除时，udev的守护进程侦听来自内核的uevent，以此添加或者删除/dev下的设备文件，所以udev只为已经连接的设备产生设备文件，而不会在/dev下产生大量虚无的设备文件。

# lun的概念 
lun的全称是logical unit number，也就是逻辑单元号。我们知道scsi总线上可挂接的设备数量是有限的，一般为6个或者15个，我们可以用target ID(也有称为scsi id的)来描述这些设备，设备只要一加入系统，就有一个代号，我们在区别设备的时候,只要说几号几号就ok了。 
而实际上我们需要用来描述的对象，是远远超过该数字的，于是我们引进了lun的概念，也就是说lun id的作用就是扩充了target id。每个target下都可以有多个lun device，我们通常简称lun device为lun，这样就可以说每个设备的描述就有原来的target x变成target x lun y了,那么显而易见的,我们描述设备的能力增强了.就好比,以前你给别人邮寄东西,写地址的时候,可以写: 
   xx市人民大街54号 xxx(收) 
但是自从高楼大厦越来越多,你不得不这么写: 
   xx市人民大街54号xx大厦518室 xxx (收) 
所以我们可以总结一下,lun就是我们为了使用和描述更多设备及对象而引进的一个方法而已,一点也没什么特别的地方. 

# Sql*plus是一个最常用的工具，具有很强的功能，主要有：
1. 数据库的维护，如启动，关闭等，这一般在服务器上操作。
2. 执行sql语句执行pl/sql。
3. 执行sql脚本。
4. 数据的导出，报表。
5. 应用程序开发、测试sql/plsql。
6. 生成新的sql脚本。
7. 供应用程序调用，如安装程序中进行脚本的安装。
8. 用户管理及权限维护等。

# SRVCTL命令介绍
SRVCTL控制RAC数据库中instance,lister,service，SRVCTL命令在oracle用户下执行
     SRVCTL实现sttart/stop/check所有实例
     $srvctl start|stop|status database -d 


     start/stop指定实例
     $srvctl start|stop|status instance -d -i 


     列出当前RAC下所有的信息
     $srvctl config database -d 


     start/stop/check所有的nodeapps ，比如VIP，GSD， listener， ONS
     $srvctl start|stop|status nodeapps -n 


     SRVCTL命令start/stop  ASM实例
     $srvctl start|stop asm -n [-i ] [-o]


     获取所有环境信息
     $srvctl getenv database -d [-i ]


     设置全局环境和变量
     $srvctl setenv database -d -t  LANG=en


     从OCR中删除已有的数据库
     $srvctl remove database -d 


     向OCR中添加一个数据库
     $srvctl add database -d  -o 
       [-m ] [-p ] [-A /netmask>] [-r {PRIMARY} |
        PHYSICAL_STANDBY |LOHICAL_STANDBY]  [-s ]


     向OCR中添加一个数据库的实例：
     $ srvctl add instance -d -i -n 
     $ srvctl add instance -d -i -n 


     向OCR中添加一个ASM实例：
     $ srvctl add asm -n -i -o 


     添加一个service
     $ srvctl add service -d -s -r [-a
       ] [-P ] [-u]
 
     修改在其他节点上的service
     $ srvctl modify service -d -s -I -t
       
      
    relocate某个节点的service到其他节点
     $ srvctl relocate service -d -s -I

# Oracle ACFS resource
The Oracle ACFS file system resource is supported only for Oracle Grid Infrastructure cluster configurations; it is not supported for Oracle Restart configurations


# acfsutil
[Fri Aug 18 01:11:38][10568][root@rwsae01.us.oracle.com:~][0]# acfsutil -h
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


. Displaying help for Oracle ACFS acfsutil commands
$ /sbin/acfsutil -h repl info 
$ /sbin/acfsutil repl info -h

. Displaying Oracle ACFS Version Information
[rwsae01]:[01:18:55]:[/root]:$ /sbin/acfsutil version
acfsutil version: 12.2.0.2.0

# replication
主要的目的是 在两套不同的cluster（比如 Linux 和 Solaris）上建两套 ACFS
一个在北京 一个在深圳
互相备份

# Node-Local or Clusterwide File Systems
Node-local

This file system type is limited to the number of nodes it can mount on. Depending on if it is created with SRVCTL or acfsutil commands, it may only mount on one node, a subset of nodes, or all the configured nodes. In some cases, this could look the same as a full cluster configuration, but if new nodes are added to the cluster, the file system is not automatically mounted on them without modifying the list of allowable nodes.

Clusterwide

This type of file system mounts on all nodes of the cluster, with no exceptions. When new members are added to the cluster, the file system is automatically available on them. This type of resource is required for certain configurations, such as Oracle Database or Oracle HANFS.
