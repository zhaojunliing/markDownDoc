```sql
--查询某一表所占空间
select segment_name,tablespace_name,bytes B,bytes/1024 KB,bytes/1024/1024 MB,partition_name 
from user_segments a 
where segment_name = 'T_S_TRAIN_LKJ'
AND tablespace_name = 'WF_XL_ZZ';


alter table tablename enable row movement;--打开行移动

alter table tablename shrink space cascade;--压缩表及相关数据段，并下调HWM

alter table tablename shrink sapce compact;--只压缩，不下调HWM

alter table tablename shrink sapce ;--下调HWM

alter table tablename disable row movement;--关闭行移动


--查看可以调整数据文件大小的最小大小
SELECT f.FILE_NAME,
       f.TABLESPACE_NAME,
       CEIL((NVL(e.hwm, 1) * 8192) / 1024 / 1024) as "Mb"
  FROM dba_data_files f
  LEFT JOIN (SELECT file_id, max(block_id + blocks - 1) hwm
               FROM dba_extents
              GROUP BY file_id) e
    ON f.file_id = e.file_id;

--缩减表空间大小
ALTER DATABASE DATAFILE '/your/path/your_file01.dbf' RESIZE 256M;    

--查询数据块大小
 SELECT FILE#,NAME,BLOCK_SIZE FROM V$DATAFILE;
 
--查看可以调整数据文件大小的最小大小
SELECT a.tablespace_name,       
       a.file_name,       
       round(a.bytes / 1024 / 1024 / 1024, 2) AS "current_bytes(GB)",  
       round(a.bytes / 1024 / 1024 / 1024 -
             b.resize_to / 1024 / 1024 / 1024,
             2) AS "shrink_by_bytes(GB)",       
       round(b.resize_to / 1024 / 1024 / 1024, 2) AS "resize_to_bytes(GB)"
  FROM dba_data_files a,       
       (SELECT file_id,
               MAX((block_id + blocks - 1) * &v_block_size) AS resize_to        
          FROM dba_extents        
         GROUP by file_id) b
 WHERE a.file_id = b.file_id      
   and a.TABLESPACE_NAME in      
       (        
        'WF_XL_ZZ'       
        )
 ORDER BY a.tablespace_name, a.file_name;
```

