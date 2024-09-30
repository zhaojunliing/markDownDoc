Oracle清理日志命令

```shell
find /home/oracle/app/admin/orcl/adump -mtime +2 -name "*.aud" | xargs rm -f 
find /home/oracle/app/diag/rdbms/orcl/orcl/trace -mtime +2 -name "*.trc" | xargs rm -f 
find /home/oracle/app/diag/rdbms/orcl/orcl/trace -mtime +2 -name "*.trm" | xargs rm -f 
find /home/oracle/app/diag/rdbms/orcl/orcl/alert -mtime +2 -name "log_*.xml" | xargs rm -f 
find /home/oracle/app/diag/tnslsnr/ai_database/listener/alert -mtime +2 -name "log_*.xml" | xargs rm -f 
echo "" > /home/oracle/app/diag/tnslsnr/ai_database/listener/trace/listener.log
```

