# VNC启动报错

VNC安装BD software ,启动runInstaller 
```
19:15:04 Wenzhe Shi: [rwsae01]:[04:13:22]:[/scratch/oracle/OraHome]:$ ./runInstaller -debug -loglevel detailed -printtime

ERROR: Unable to verify the graphical display setup. This application requires X display. Make sure that xdpyinfo exist under PATH variable.

No protocol specified

Can't connect to X11 window server using ':1' as the value of the DISPLAY variable.
```

成因：GI（crsusr）,BD Software(racusr)
以crsusr启动VNC 在Terminal里切换到racusr调用runInstaller.sh时容易报上述错误。
解决方式
1. xhost +  (export DISPLAY=rwsae01:1)
2. 在terminal里切换到racusr下，然后另外启动一个vnc，调用./runInstaller -debug -loglevel detailed -printtime

# VNCServer 卡死
xkill 是在桌面用的杀死图形界面的程序。比如当firefox 出现崩溃不能退出时，点
鼠标就能杀死firefox 。当xkill运行时出来和个人脑骨的图标，哪个图形程序崩溃一点就

OK了。如果您想终止xkill ，就按右键取消。
[Thu Aug 24 19:09:33][3142][root@rwsae01.us.oracle.com:~][0]# xkill
Select the window whose client you wish to kill with button 1....
xkill:  killing creator of resource 0x1600053
