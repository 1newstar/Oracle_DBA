# 脚本(稍作修改yuyao)
节点rwsae01.us.oracle.com /scratch/crsusr/yuyao
1. -rwxr-xr-x 1 crsusr oper   159 Aug  9 01:43 dd5356.sh
2. -rwxr-xr-x 1 crsusr oper  2146 Aug  9 02:43 dein_lnx_bak.sh
3. -rwxr-xr-x 1 crsusr oper  2269 Aug  9 01:34 deinstall_linux.sh
(deinstall脚本需要先从 para.cfg 调取参数)
运行过deinstall_linux.sh后，需要对磁盘进行dd操作：
dd if=/dev/zero of=/dev/bdisk/qiandiskd3 bs=1024 count=1024
dd if=/dev/zero of=/dev/bdisk/qiandiske3 bs=1024 count=1024
dd if=/dev/zero of=/dev/bdisk/qiandiskf3 bs=1024 count=1024
> 需要复制 deinstall_linux.sh 到所有节点，进行清理。

4. -rwxr-xr-x 1 crsusr oper  2388 Aug  9 01:36 logcheck.sh
5. -rwxr-xr-x 1 crsusr oper   479 Aug  9 01:34 para.cfg
（一些配置文件）
6. -rwxr-xr-x 1 crsusr dba  10782 Aug  9 01:46 set_myenv.sh
7. -rwxr-xr-x 1 crsusr oper  2527 Aug  9 01:35 shiphome.sh
8. -rwxr-xr-x 1 crsusr oper 1284308 Aug 10 20:32 e.sh


## 1. dd5356.sh
```
for x in a3 b3 c3 d3 e3 f3 g3 h3 i3 j3 k3 l3
do
    dd if=/dev/zero of=/dev/bdisk/qiandisk$x bs=1024 count=1024
done

ls -l /dev/bdisk/qiandisk*
```

## 2. dein_lnx_bak.sh
```
#/bin/sh
root=`id|grep uid|awk '{print $1}'|grep root`

if [ ! $root ];then
   echo "You need run as root user";
   exit
fi


WPATH=/Manager
PARFILE=$WPATH/env/para.cfg

if [ -f "$PARFILE" ];then
GRID_HOME=`sed -n '4p' $PARFILE|awk '{print $2}'`
ORA_HOME=`sed -n '5p' $PARFILE|awk '{print $2}'`
GRID_BASE=`sed -n '6p' $PARFILE|awk '{print $2}'`
ORA_BASE=`sed -n '7p' $PARFILE|awk '{print $2}'`
ORAINVENTORY=`sed -n '8p' $PARFILE|awk '{print $2}'`
GIUSR=`sed -n '9p' $PARFILE|awk '{print $2}'`
GIGRP=`sed -n '10p' $PARFILE|awk '{print $2}'`
RACUSR=`sed -n '12p' $PARFILE|awk '{print $2}'`
RACGRP=`sed -n '13p' $PARFILE|awk '{print $2}'`

else
GRID_HOME=/scratch/oracle/CrsHome
ORA_HOME=/scratch/oracle/OraHome
ORA_BASE=/scratch/oracle/base
GRID_BASE=/scratch/oracle/crsbase
ORAINVENTORY=/scratch/oracle/oraInventory

fi

RSP_DIR=$WPATH/conf
export ORACLE_HOME=$GRID_HOME
$GRID_HOME/bin/crsctl stop crs -f > /tmp/crsstop.out
count1=`egrep  "CRS-4133|CRS-4639" /tmp/crsstop.out`
echo $count1;

$GRID_HOME/bin/acfsload stop
$GRID_HOME/bin/acfsroot uninstall
$GRID_HOME/bin/asmcmd afd_deconfigure


echo "==== Start delete files ===="
cp $GRID_HOME/assistants/dbca.rsp $RSP_DIR/
cp $ORA_HOME/install/response/db_*.rsp $RSP_DIR/

set -x

rm -rf $GRID_HOME
rm -rf $ORA_HOME
rm -rf $ORA_BASE
rm -rf $GRID_BASE
rm -rf $ORAINVENTORY

rm -rf /etc/init.d/init.ohasd
rm -rf /etc/init.d/ohasd
rm -rf /etc/rc2.d/K19ohasd
rm -rf /etc/rc3.d/S96ohasd
rm -rf /etc/rc5.d/S96ohasd
rm -rf /var/tmp/.oracle
rm -rf /tmp/.oracle
rm -rf /etc/ora*
rm -rf /etc/ORCL*

cp  /etc/inittab /etc/bak.inittab.suse
sed -i /init\.ohasd/d  /etc/inittab

mkdir -p $GRID_HOME
mkdir -p $ORA_BASE $ORA_HOME
mkdir -p $GRID_BASE
mkdir -p $ORAINVENTORY

chown -R $GIUSR:$GIGRP $GRID_HOME $GRID_BASE $ORAINVENTORY
chown -R $RACUSR:$RACGRP $ORA_BASE $ORA_HOME

chmod 775 $GRID_HOME $GRID_BASE $ORA_BASE $ORAINVENTORY $ORA_HOME

for x in $GRID_HOME $ORA_HOME $ORA_BASE $GRID_BASE $ORAINVENTORY
do
ret=`ls -l $x`
if [ "$ret1" != "total 0" ];then
  rm -rf $x/*
fi
done

ls -ld $GRID_HOME $ORA_HOME $ORA_BASE $GRID_BASE $ORAINVENTORY
ls -l $GRID_HOME $ORA_HOME $ORA_BASE $GRID_BASE $ORAINVENTORY
```


## 3. deinstall_linux.sh
```
#/bin/sh
root=`id|grep uid|awk '{print $1}'|grep root`

if [ ! $root ];then
   echo "You need run as root user";
   exit
fi


WPATH=/Manager
PARFILE=$WPATH/env/para.cfg

if [ -f "$PARFILE" ];then
GRID_HOME=`sed -n '4p' $PARFILE|awk '{print $2}'`
ORA_HOME=`sed -n '5p' $PARFILE|awk '{print $2}'`
GRID_BASE=`sed -n '6p' $PARFILE|awk '{print $2}'`
ORA_BASE=`sed -n '7p' $PARFILE|awk '{print $2}'`
ORAINVENTORY=`sed -n '8p' $PARFILE|awk '{print $2}'`
GIUSR=`sed -n '9p' $PARFILE|awk '{print $2}'`
GIGRP=`sed -n '10p' $PARFILE|awk '{print $2}'`
RACUSR=`sed -n '12p' $PARFILE|awk '{print $2}'`
RACGRP=`sed -n '13p' $PARFILE|awk '{print $2}'`

else
GRID_HOME=/scratch/oracle/CrsHome
ORA_HOME=/scratch/oracle/OraHome
ORA_BASE=/scratch/oracle/base
GRID_BASE=/scratch/oracle/crsbase
ORAINVENTORY=/scratch/oracle/oraInventory

fi

RSP_DIR=$WPATH/conf
export ORACLE_HOME=$GRID_HOME
$GRID_HOME/bin/crsctl stop crs -f > /tmp/crsstop.out
count1=`egrep  "CRS-4133|CRS-4639" /tmp/crsstop.out`
echo $count1;

$GRID_HOME/bin/acfsload stop
$GRID_HOME/bin/acfsroot uninstall
$GRID_HOME/bin/asmcmd afd_deconfigure

if [ -n "$count1" ];then

echo "==== Start delete files ===="
cp $GRID_HOME/assistants/dbca.rsp $RSP_DIR/
cp $ORA_HOME/install/response/db_*.rsp $RSP_DIR/

set -x

rm -rf $GRID_HOME
rm -rf $ORA_HOME
rm -rf $ORA_BASE
rm -rf $GRID_BASE
rm -rf $ORAINVENTORY

rm -rf /etc/init.d/init.ohasd
rm -rf /etc/init.d/ohasd
rm -rf /etc/rc2.d/K19ohasd
rm -rf /etc/rc3.d/S96ohasd
rm -rf /etc/rc5.d/S96ohasd
rm -rf /var/tmp/.oracle
rm -rf /tmp/.oracle
rm -rf /etc/ora*
rm -rf /etc/ORCL*

cp  /etc/inittab /etc/bak.inittab.suse
sed -i /init\.ohasd/d  /etc/inittab

mkdir -p $GRID_HOME
mkdir -p $ORA_BASE $ORA_HOME
mkdir -p $GRID_BASE
mkdir -p $ORAINVENTORY

chown -R $GIUSR:$GIGRP $GRID_HOME $GRID_BASE $ORAINVENTORY
chown -R $RACUSR:$RACGRP $ORA_BASE $ORA_HOME

chmod 775 $GRID_HOME $GRID_BASE $ORA_BASE $ORAINVENTORY $ORA_HOME

for x in $GRID_HOME $ORA_HOME $ORA_BASE $GRID_BASE $ORAINVENTORY
do
ret=`ls -l $x`
if [ "$ret1" != "total 0" ];then
  rm -rf $x/*
fi
done

ls -ld $GRID_HOME $ORA_HOME $ORA_BASE $GRID_BASE $ORAINVENTORY
ls -l $GRID_HOME $ORA_HOME $ORA_BASE $GRID_BASE $ORAINVENTORY

else
     echo "======== Deinstall not finished,pls try again ====="
     cat /tmp/crsstop.out
fi
```


## 4. logcheck.sh
```
#!/bin/bash
#===================================
# Unix Porting code here
#=====================
 PS1="[\h]:[\t]:[\w]:\$ "
export PS1

UNAME="/bin/uname"
PLATFORM=`$UNAME`
case $PLATFORM in
  Linux | AIX )
    orainst="/etc/oraInst.loc";;
  HP-UX | SunOS )
    orainst="/var/opt/oracle/oraInst.loc";;
  *)
    orainst=""
    echo "ERROR: Unknown OS";;
esac
hostname=`hostname`


if [ -f ${orainst} ]; then

  ora_inventory=`cat $orainst | grep "inventory_loc" | cut -d "=" -f2`
  ora_cont="$ora_inventory/ContentsXML/inventory.xml"
  ch=`cat $ora_cont | grep 'IDX="1"' | grep -i "GI" | cut -d '"' -f4`
  oh=`cat $ora_cont | grep 'IDX="2"' | cut -d '"' -f4`
  cb=`cat $ch/crs/install/crsconfig_params | grep "ORACLE_BASE=" | cut -d "=" -f2` 
  ob=`if [ $oh ]; then cat $oh/install/envVars.properties | grep "ORACLE_BASE=" | cut -d "=" -f2 ; fi`
  tp=`crsctl get cluster configuration|grep Class|awk '{print $3}'`
  dsc=`crsctl get cluster class|grep omain`

  ERR='died unexpectedly|evict|ORA-07445|ORA-00600|ORA-00494|ORA-00240|ORA-29702|ORA-29740|ORA-29770|ORA-15082|ORA-00481|ORA-00700|ORA-32701|ORA-00227|ORA-00202|ORA-04031'

  for node in `$ch/bin/olsnodes -n|awk '{print $1}'`
  do
      echo "==================$node================"
      ASM_LOG=$cb/diag/asm/+asm/+ASM*/trace/alert_+ASM*.log
      IOS_LOG=$cb/diag/ios/+ios/+IOS*/trace/alert_+IOS*.log
      APX_LOG=$cb/diag/apx/+apx/+APX*/trace/alert_+APX*.log
   
      # If this is member cluster node, no need to check asm/ios/apx log

      if [ "$tp" != "Member" ];then
        ssh $node "ls $ASM_LOG;egrep '$ERR' $ASM_LOG|sort |uniq -c"

        # If this is not a domain server cluster node, no need to check ios log

        if [  -n "$dsc" ];then
          ssh $node "ls $IOS_LOG;egrep '$ERR' $IOS_LOG|sort |uniq -c"
        fi
        ssh $node "ls $APX_LOG;egrep '$ERR' $APX_LOG|sort |uniq -c"
      fi

      # Check all the database logs
      for dname in `ssh $node "ls $ob/diag/rdbms |grep -v _mgmtdb"`
      do
          dbname=$dname

          # Check all the instances logs for one database name
          for sidnum in `ssh $node "ls $ob/diag/rdbms/$dbname |grep -v mif"`
          do
             DB_LOG=$ob/diag/rdbms/$dbname/$sidnum/trace/alert_*.log
             ssh $node "ls $DB_LOG;egrep '$ERR' $DB_LOG|sort |uniq -c"
          done
      done
  done
fi
```


## 5. para.cfg
```
SHIPHOME        /net/adcnas469/export/farm_metadata/RDBMS_MAIN_LINUX.X64_T20528234/install/shiphome
SHIPVER         H_170726
SOFT_LOC        /scratch/oracle/soft
GRID_HOME       /scratch/oracle/CrsHome
ORA_HOME        /scratch/oracle/OraHome
GRID_BASE       /scratch/oracle/crsbase
ORA_BASE        /scratch/oracle/orabase
ORAINVENTORY    /scratch/oracle/oraInventory
GIUSR           crsusr
GIGRP           oinstall
GIRSP           grid.rsp
RACUSR          racusr
RACGRP          oinstall
RACRSP          db.rsp
DBCARSP         dbca.rsp
DB_NAME         orcl
```

## 6. set_my_env.sh
```
#!/bin/bash
# Warning: 1. Pls. DO NOT modify this script!
#          2. DO NOT add ANY "exit/return" code into this script!   
#===================================================
# Unix Porting code here
#===================================================
 PS1="[\h]:[\t]:[\w]:\$ "
export PS1

UNAME="/bin/uname"
PLATFORM=`$UNAME`
case $PLATFORM in
  Linux | AIX )
    orainst="/etc/oraInst.loc";;
  HP-UX | SunOS )
    orainst="/var/opt/oracle/oraInst.loc";;
  *)
    orainst=""
    echo "ERROR: Unknown OS";;
esac
hostname=`hostname`


if [ -f ${orainst} ]; then

  ora_inventory=`cat $orainst | grep "inventory_loc" | cut -d "=" -f2`
  ora_cont="$ora_inventory/ContentsXML/inventory.xml"
  ch=`cat $ora_cont | grep 'IDX="1"' | grep -i "GI" | cut -d '"' -f4`
  oh=`cat $ora_cont | grep 'IDX="2"' | cut -d '"' -f4`
  cb=`cat $ch/crs/install/crsconfig_params | grep "ORACLE_BASE=" | cut -d "=" -f2` 
  ob=`if [ $oh ]; then cat $oh/install/envVars.properties | grep "ORACLE_BASE=" | cut -d "=" -f2 ; fi`
  
  #crs_owner=`ls -ld ${ora_inventory} | awk -F' ' '{print $3}'`  # get the CRS_OWNER as owner of ora_inventory, maybe there's better way to do this
  crs_owner=`cat $ch/crs/install/crsconfig_params | grep "ORACLE_OWNER=" | cut -d "=" -f2`

  alias ch="ORACLE_HOME=${ch} && export ORACLE_HOME"
  alias oh="ORACLE_HOME=${oh} && export ORACLE_HOME"
  
#  alias crslog="mydate && cd $ch/log/${hostname} && pwd && ls -lt"
  alias crslog="mydate && cd $cb/diag/crs/${hostname}/crs && pwd && ls -lt"
  alias asmlog="mydate && cd ${cb}/diag/asm/+asm/+ASM* && pwd && ls -lt"
  alias alog="mydate && tail -f ${cb}/diag/asm/+asm/+ASM*/trace/alert*.log"
  alias plog="mydate && tail -f ${cb}/diag/apx/+apx/+APX*/trace/alert*.log"
  alias slog="mydate && tail -f ${cb}/diag/ios/+ios/+IOS*/trace/alert*.log"

  alias dlog="mydate && tail -f ${ob}/diag/rdbms/tdb*//trace/alert*.log"
 
  asmsid="+ASM"`ps -eo comm |grep asm_pmon |grep -v grep|cut -b14-`;
  apxsid="+APX"`ps -eo comm  |grep apx_pmon |grep -v grep|cut -b14-`;
  ixid="+IOS"`ps -eo comm  |grep ios_pmon |grep -v grep|cut -b14-`;
  dbsid=`ps -eo comm |grep ora_pmon |grep -v grep|cut -b10-`; 
  alias asql="export ORACLE_HOME=$ch && export ORACLE_SID=$asmsid && ${ch}/bin/sqlplus \"/ as sysasm\" "
  alias dsql="export ORACLE_HOME=$oh && export ORACLE_SID=$dbsid && ${oh}/bin/sqlplus \"/ as sysdba\""
  alias psql="export ORACLE_HOME=$ch && export ORACLE_SID=$apxsid && ${ch}/bin/sqlplus \"/ as sysasm\""
  alias issql="export ORACLE_HOME=$ch && export ORACLE_SID=$ixid && ${ch}/bin/sqlplus \"/ as sysasm\" "

  alias crsalert="mydate && cd $ch/log/${hostname} && vi alert${hostname}.log"
  alias asmalert="mydate && cd ${cb}/diag/asm/+asm/+ASM*/trace/ && vi alert*.log"
  dbalert () { date && cd ${ob}/diag/rdbms/${1}/*${2}/trace/ && vi alert*.log ; } # Usage: dbalert db_name [inst_num]
  tdbalert () { date && cd ${ob}/diag/rdbms/${1}/*${2}/trace/ && tail -f alert*.log ; } # Usage: dbalert db_name [inst_num]
  dbalertacfs () { date && cd /ee/oracle/acfs/diag/rdbms/${1}/*${2}/trace/ && vi alert*.log ; } # Usage: dbalert db_name [inst_num]
  dbalertacfs2 () { date && cd /ee/oracle/acfs2/diag/rdbms/${1}/*${2}/trace/ && vi alert*.log ; } # Usage: dbalert db_name [inst_num]
  
  alias asmcalog="mydate && cd ${cb}/cfgtoollogs/asmca && pwd && ls -lt"
  alias netcalog="mydate && cd ${cb}/cfgtoollogs/netca && pwd && ls -lt"
  alias dbcalog="mydate && cd ${ob}/cfgtoollogs/dbca && pwd && ls -lt"
  alias emcalog="mydate && cd ${ob}/cfgtoollogs/emca && pwd && ls -lt"
  
  alias ohasd="mydate && cd $ch/log/${hostname}/ohasd && vi ohasd.log"
  alias ohasdout="mydate && cd $ch/log/${hostname}/ohasd && vi ohasdOUT.log"
  alias cssd="mydate && cd $ch/log/${hostname}/cssd && vi ocssd.log"
  alias cssdout="mydate && cd $ch/log/${hostname}/cssd && vi cssdOUT.log"
  alias ctssd="mydate && cd $ch/log/${hostname}/ctssd && vi octssd.log"
  alias ctssdout="mydate && cd $ch/log/${hostname}/ctssd && vi ctssdOUT.log"
  alias crsd="mydate && cd $ch/log/${hostname}/crsd && vi crsd.log"
  alias crsdout="mydate && cd $ch/log/${hostname}/crsd && vi crsdOUT.log"
  alias gnsd="mydate && cd $ch/log/${hostname}/gnsd && vi gnsd.log"
  alias gnsdout="mydate && cd $ch/log/${hostname}/gnsd && vi gnsdOUT.log"
  alias gipcd="mydate && cd $ch/log/${hostname}/gipcd && vi gipcd.log"
  alias gipcdout="mydate && cd $ch/log/${hostname}/gipcd && vi gipcdOUT.log"  
  alias gpnpd="mydate && cd $ch/log/${hostname}/gpnpd && vi gpnpd.log"
  alias gpnpdout="mydate && cd $ch/log/${hostname}/gpnpd && vi gpnpdOUT.log"
  alias evmd="mydate && cd $ch/log/${hostname}/evmd && vi evmd.log"
  alias evmdout="mydate && cd $ch/log/${hostname}/evmd && vi evmdOUT.log"
  alias mdnsd="mydate && cd $ch/log/${hostname}/mdnsd && vi mdnsd.log"
  alias mdnsdout="mydate && cd $ch/log/${hostname}/mdnsd && vi mdnsdOUT.log"

  alias agent1="mydate && cd $ch/log/${hostname}/agent/ohasd && pwd && ls -lt"
  alias agent2="mydate && cd $ch/log/${hostname}/agent/crsd && pwd && ls -lt"
  alias cagent1="mydate && cd $ch/log/${hostname}/agent/ohasd/oraagent_${crs_owner} && vi oraagent_${crs_owner}.log"
  alias cagent2="mydate && cd $ch/log/${hostname}/agent/crsd/oraagent_${crs_owner} && vi oraagent_${crs_owner}.log"
  alias ragent1="mydate && cd $ch/log/${hostname}/agent/ohasd/orarootagent_root && vi orarootagent_root.log"
  alias ragent2="mydate && cd $ch/log/${hostname}/agent/crsd/orarootagent_root && vi orarootagent_root.log"
  alias sagent2="mydate && cd $ch/log/${hostname}/agent/crsd/scriptagent_${crs_owner} && vi scriptagent_${crs_owner}.log"
  
  alias cvu="mydate && cd $ch/log/${hostname}/cvu && pwd && ls -ltR"
  alias cvulog="mydate && cd $ch/log/${hostname}/cvu/cvulog && vi cvu*.log"
  alias cvutrc="mydate && cd $ch/log/${hostname}/cvu/cvutrc && vi cvutrace.log.0"
  
  alias orainv="mydate && cd ${ora_inventory} && pwd && ls -lt"
  alias client="mydate && cd $ch/log/${hostname}/client && pwd && ls -lt"
  alias dbs="mydate && cd ${oh}/dbs && pwd && ls -lt"
  
  sid () { date && ORACLE_SID="${1}" && export ORACLE_SID ; } # Usage: sid { db_inst | asm_inst }
  sql () { date && if [[ $1 == +ASM* ]]; then ORACLE_HOME=${ch} && export ORACLE_HOME; else ORACLE_HOME=${oh} && export ORACLE_HOME; fi && ORACLE_SID="${1}" && export ORACLE_SID && sqlplus '/ as sysdba' ; } # Usage: sql { db_inst | asm_inst }
  
  crs_owner=
  ora_cont=
  ora_inventory=
  
  PATH=/usr/local/git/bin:/usr/local/git/libexec/git-core:$PATH:${ch}/bin:${oh}/bin:/usr/dev_infra/platform/bin && export PATH

fi

orainst=

PATH=$PATH:/sbin:~
export PATH
# other quick accesses I get from http://www.pixelbeat.org/scripts/ls_color/
alias al="alias"
alias ..="cd .."
alias ...="cd ../.."
alias ....="cd ../../.."
alias -- -="cd -"
alias l.="mydate && ls -d .*"
alias ll="mydate && ls -lt"
alias lh="mydate && ls -lh"
alias mydate='echo Local Time Now: `date "+%Y-%m-%d %H:%M:%S"`'
#alias ls='ls --color=auto'
#alias ll='ls -lhvrt'
#alias lld='ls -lUd */'
md () { mkdir -p "$1" && cd "$1" ; } # Usage: md dir_name

# Add by Yuyao
WORKPATH=/Manager
SOFT_LOC=/scratch/oracle/soft
giowner=`cat $ch/crs/install/crsconfig_params | grep "ORACLE_OWNER=" | cut -d "=" -f2`

alias soft='cd $SOFT_LOC'
alias tenv='cd $WORKPATH/env'
alias tdaily='cd $WORKPATH/daily'
alias tcase='cd $WORKPATH/case'
alias net="cd $oh/network/admin"
alias yy='cd $HOME/yuyao'
alias ddlab='cd $HOME/yuyao/dd_label'

alias dbtp='cd $SOFT_LOC/dbworkload/aroltp_run'
alias dbcf='cd $SOFT_LOC/dbworkload/cf'
alias cdc='cd $SOFT_LOC/cdcasm_workload'
alias hconf='cd $SOFT_LOC/hornet/config'
alias hlog='cd $SOFT_LOC/hornet/log/`hostname`'
alias hcase='cd $SOFT_LOC/hornet/asmcase'

alias smon='ps -ef|grep _smon_ |grep -v grep'
alias crs='crsctl check crs'
alias res='crsctl stat res -t'
alias dp='export DISPLAY=`hostname`:1.0'
alias sroot='/usr/local/packages/aime/install/run_as_root bash'
alias master='sh $WORKPATH/daily/checkmaster.sh'
alias hasver='$ch/bin/oclumon debug version'

alias log='sh $WORKPATH/daily/logcheck.sh'
alias asmcmdlog="mydate && cd ${ch}/log/diag/asmcmd/user_$giowner/$hostname/alert && pwd && ls -lt"
alias ioslog="mydate && cd ${cb}/diag/ios/+ios/+IOS* && pwd && ls -lt"
alias apxlog="mydate && cd ${cb}/diag/apx/+apx/+APX* && pwd && ls -lt"
alias asmcmd="export ORACLE_HOME=$ch && export ORACLE_SID=$asmsid && ${ch}/bin/asmcmd"
alias crsenv="export ORACLE_HOME=$ch && export ORACLE_SID=$asmsid && export PATH=$ch/bin:$PATH"
alias dbenvorcl="export ORACLE_HOME=$oh && export ORACLE_SID=$dbsidorcl && export PATH=$oh/bin:$PATH"
alias dbenvzyy="export ORACLE_HOME=$oh && export ORACLE_SID=$dbsidzyy && export PATH=$oh/bin:$PATH"
alias dbenv="export ORACLE_HOME=$oh && export ORACLE_SID=$dbsid && export PATH=$oh/bin:$PATH"

dbsql () { export ORACLE_HOME=$oh && export ORACLE_SID=${1} && ${oh}/bin/sqlplus \"/ as sysdba\";} # Usage: dbsql instance_name
dblog () { date && cd /scratch/oracle/orabase/diag/rdbms/${1}/*${2}/trace/ && tail -f alert*.log ; } # Usage: dblog dbname [inst_n]
vidblog () { date && cd /scratch/oracle/orabase/diag/rdbms/${1}/*${2}/trace/ && vi alert*.log ; } # Usage: dblog dbname [inst_n]

alias rlog='cd $cb/crsdata/$hostname/crsconfig'
alias cdcbas='cd /scratch/oracle/Automation/cdcasm-workload'
alias cdccfg='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload'
alias cdcsrc='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload/src'
alias cdcsan='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload/src/Sanity_Check'
alias cdcpkg='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload/pkg'
alias cdcasm='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload/src/ASM_function_unit'
alias cdcwrk='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload/src/Workload'
alias cdcdes='cd /scratch/oracle/Automation/cdcasm-workload/cdcasm_workload/src/Destructive_function_unit'

alias afdls='asmcmd afd_lsdsk'
alias dskcan='asmcmd lsdsk --candidate'
alias dskmem='asmcmd lsdsk --member'
alias lsdg='asmcmd lsdg'

alias dirset='echo b > /proc/sysrq-trigger'

#For install/deinstall
alias delnx='sh $WORKPATH/env/deinstall_linux.sh'
alias desp='sh $WORKPATH/env/deinstall_solarissp64.sh'
alias deso='sh $WORKPATH/env/deinstall_solarisx64.sh'
alias shiphome='sh $WORKPATH/env/getlabel.sh'
alias giinstall='/scratch/oracle/CrsHome/gridSetup.sh  -silent -responseFile /scratch/crsusr/grid.rsp -ignorePrereqFailure'
alias dbinstall='/scratch/oracle/OraHome/runInstaller  -silent -responseFile /scratch/racusr/db.rsp -ignorePrereqFailure'
alias dbcaorcl='/scratch/oracle/OraHome/bin/dbca -CreateDatabase  -silent -responseFile /scratch/racusr/dbca_orcl.rsp -ignorePrereqFailure'
alias dbcazyy='/scratch/oracle/OraHome/bin/dbca -CreateDatabase  -silent -responseFile /scratch/racusr/dbca_zyy.rsp -ignorePrereqFailure'
```

## 7. shiphome.sh
```
#!/bin/bash

#####################################################################################################################
# Usage: 
#      shiphome.sh 
#
#  Run as root user
#
# Input:
#      Get parameter from file para.cfg
#
# Author:  yuyao.zheng@oracle.com
#
# Description:
#           - Get goldimage label from location set by para.cfg
#           - Unzip GRID AND DB software zip file to location set by para.cfg
#
#    MODIFIED        (MM/DD/YY)
#     Yuyao          2017/05/31 - Creation
######################################################################################################################
root=`id|grep uid|awk '{print $1}'|grep root`

if [ ! $root ];then
   echo "You need run as root user";
   exit
fi


WPATH=`dirname $0`
PARFILE=$WPATH/para.cfg
SHIPHOME=`sed -n '1p' $PARFILE|awk '{print $2}'`
SHIPVER=`sed -n '2p' $PARFILE|awk '{print $2}'`
SOFT_LOC=`sed -n '3p' $PARFILE|awk '{print $2}'`
GIUSR=`sed -n '9p' $PARFILE|awk '{print $2}'`
GIGRP=`sed -n '10p' $PARFILE|awk '{print $2}'`
RACUSR=`sed -n '12p' $PARFILE|awk '{print $2}'`
RACGRP=`sed -n '13p' $PARFILE|awk '{print $2}'`
GRID_HOME=`sed -n '4p' $PARFILE|awk '{print $2}'`
ORA_HOME=`sed -n '5p' $PARFILE|awk '{print $2}'`

mkdir $SOFT_LOC/$SHIPVER
shiploc=$SOFT_LOC/$SHIPVER
cd $SHIPHOME
unzip *database.zip "database/stage/labels.txt" -d $shiploc
cd $SHIPHOME/goldimage
cp grid_home*.zip  $shiploc
cp db_home*.zip   $shiploc
chown -R $GIUSR:$GIGRP $shiploc/grid_home*.zip
chown -R $RACUSR:$RACGRP $shiploc/db_home*.zip
chmod -R 775 $shiploc/*

gsize1=`ls -l $SHIPHOME/goldimage|grep grid_home|awk '{print $5}'`
gsize2=`ls -l $shiploc|grep grid_home|awk '{print $5}'`
dbsize1=`ls -l $SHIPHOME/goldimage|grep db_home|awk '{print $5}'`
dbsize2=`ls -l $shiploc|grep db_home|awk '{print $5}'`

echo "------ Shiphome Download Result ------"
if [ $gsize1 == $gsize2 ];then
  if [ $dbsize1 == $dbsize2 ];then
    echo "shiphome download successfully";
    ls -l $shiploc
  else
    echo "shiphome download failed, DB size is different";
    exit
  fi
else
    echo "shiphome download failed, GI size is different";
    exit
fi

ret1=`ls -l $GRID_HOME`
ret2=`ls -l $ORA_HOME`

if [ "$ret1" != "total 0" ];then
   rm -rf $GRID_HOME/*
fi

if [ "$ret2" != "total 0" ];then
   rm -rf $ORA_HOME/*
fi

su - $GIUSR << EOF
unzip -d $GRID_HOME $shiploc/grid_home.zip >/dev/null 
exit
EOF

su - $RACUSR << EOF
unzip -d $ORA_HOME $shiploc/db_home.zip >/dev/null
exit
EOF

echo "---- After shiphome unzip ----";
ls -lrt $GRID_HOME 
ls -lrt $ORA_HOME
```


## 8. e.sh (清理环境脚本)
1. 复制到每个节点 /bin 目录下分别清理
2. 运行
    . e.sh; clear_crs; sleep 10; echo -e "\a\a\a\a\a\a"; /sbin/reboot



# 查看DB有没有起来
srvctl status database -db orcl

# ASM instance 没起来
1. 首先修改/dev/shm权限
2. srvctl start asm -node rwsae01

