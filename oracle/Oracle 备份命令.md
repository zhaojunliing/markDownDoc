### Windows备份命令

    @echo offecho 正在备份Oracle数据库，请稍等….
    exp zhengzhoudong/admin@10.59.27.240:1521/linzzd tables = (PAS_BAS_INF_TRS,PAS_BAS_TRS_STOPINF) file=e:/db_bak/zhengzhoudong%date:~0,4%%date:~5,2%%date:~8,2%.dmp log=e:/db_bak/zhengzhoudong%date:~0,4%%date:~5,2%%date:~8,2%.log
    @echo 任务完成


### Linux备份命令

    umask 022
    export ORACLE_BASE=/home/oracle/app
    export ORACLE_HOME=$ORACLE_BASE/oracle/product/11.2.0/dbhome_1
    export ORACLE_SID=EISS
    export PATH=$PATH:$HOME/bin:$ORACLE_HOME/bin
    export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/usr/lib
    export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
    export NLS_LANG="SIMPLIFIED CHINESE_CHINA".ZHS16GBK

    month1=`date +%m`
    day1=`date +%d`
    year1=`date +%Y`
    
    nyr1="/home/oracle/bak/"$year1$month1$day1"db.dmp"
    echo $nyr1
    exp eiss_user/eiss_user file=$nyr1 owner=eiss_user

然后结合系统的定时任务进行备份数据。