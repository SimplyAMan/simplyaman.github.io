---
layout: default
title: How to calculate some statistics in Oracle
---

### Information about ASM

``` sql
-- загальний розмір ASM
SELECT TRUNC(SYSDATE), host_name, 
       ROUND(SUM(free_mb)/1024/1024,3) AS free_tb, 
       ROUND(SUM(total_mb)/1024/1024,3) AS total_tb 
  FROM v$asm_diskgroup
        CROSS JOIN v$instance
 GROUP BY host_name; 
```

### Size of database

``` sql
-- розмір бази даних
SELECT TRUNC(SYSDATE), 
       'b2test',
       SUM(Size_In_Tb) 
  FROM (SELECT ROUND(SUM(bytes)/1024/1024/1024/1024,2) size_in_tb
          FROM dba_data_files
        UNION
        SELECT ROUND(SUM(bytes)/1024/1024/1024/1024,2) size_in_tb
          FROM dba_temp_files);
```          
   
### Size of SGQ

``` sql   
-- SGA          
SELECT TO_NUMBER(VALUE)/1024/1024/1024 AS GB
  FROM v$parameter
 WHERE Name = 'sga_max_size'; 
``` 