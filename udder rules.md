
# 修改连接磁盘配置文件，并在Linux 7上使udev生效

路径：/etc/udev/rules.d
```
[Mon Aug 14 02:01:32][31746][root@rwsae01.us.oracle.com:/etc/udev/rules.d][0]# ls -l
total 12
-rw-r--r--. 1 root root  153 May  6  2014 60-ipath.rules
-rw-r--r--. 1 root root  709 May  8  2014 70-persistent-ipoib.rules
-rw-r--r--  1 root root 2352 Jun 22 19:09 90-oracleasm.rules
[Mon Aug 14 02:01:35][31746][root@rwsae01.us.oracle.com:/etc/udev/rules.d][0]# cat 90-oracleasm.rules 
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051eb4defb320",  SYMLINK+="bdisk/qiandiska%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051ed4defb332",  SYMLINK+="bdisk/qiandiskb%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051f04defb34a",  SYMLINK+="bdisk/qiandiskc%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051f24defb377",  SYMLINK+="bdisk/qiandiskd%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051f44defb3ba",  SYMLINK+="bdisk/qiandiske%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051f74defb3e4",  SYMLINK+="bdisk/qiandiskf%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051f94defb3fd",  SYMLINK+="bdisk/qiandiskg%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051fb4defb432",  SYMLINK+="bdisk/qiandiskh%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000051fe4defb451",  SYMLINK+="bdisk/qiandiski%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000052004defb46b",  SYMLINK+="bdisk/qiandiskj%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000052024defb48c",  SYMLINK+="bdisk/qiandiskk%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
KERNEL=="sd?[3]", SUBSYSTEM=="block", ENV{DEVTYPE}=="partition", ENV{ID_SERIAL}=="3600a0b80006e4eda000052054defb4ac",  SYMLINK+="bdisk/qiandiskl%n" , OWNER="crsusr", GROUP="oinstall", MODE="0660"
[Mon Aug 14 02:03:28][31746][root@rwsae01.us.oracle.com:/etc/udev/rules.d][0]# vi 90-oracleasm.rules 
[Mon Aug 14 02:06:44][31746][root@rwsae01.us.oracle.com:/etc/udev/rules.d][0]# udevadm control --reload-rules && udevadm trigger
```

修改完配置文件，需要做下述处理 使修改过的udev生效
方法一
```
udevadm control --reload-rules && udevadm trigger
```
方法二
```
    1.    re-load with udevadm control --reload-rules
    2.    re-trigger with udevadm trigger --attr-match=subsystem=net
```

