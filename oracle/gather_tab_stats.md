---
layout: default
title: Gather table statistics
---
## Отримання інформації як змінилась таблиця з часу останнього збору статистики

``` sql
SELECT *
  FROM dba_tab_modifications
 WHERE Table_Owner = USER
   and Table_Name like '%DBT_%' ;
```

## Збір статистики
### По таблиці

``` sql
BEGIN 
  dbms_stats.gather_table_stats (  
    ownname => USER, 
    tabname => 'ARC_FILEF7F8_DETAIL',  
    estimate_percent => dbms_stats.AUTO_SAMPLE_SIZE, 
    DEGREE => DBMS_STATS.DEFAULT_DEGREE,  
    granularity => 'ALL',  
    CASCADE => TRUE 
  ); 
END;
/
```

або просто:

``` sql
exec dbms_stats.gather_table_stats('CREATOR' , 'AACCOUNT', cascade => TRUE);
```

### По партиції

``` sql
BEGIN 
  dbms_stats.gather_table_stats (  
    ownname => USER, 
    tabname => 'ARC_BALANCE',  
    partname => 'PM_200906',  
    estimate_percent => dbms_stats.AUTO_SAMPLE_SIZE, 
    DEGREE => DBMS_STATS.DEFAULT_DEGREE, 
    granularity => 'PARTITION',  
    CASCADE => TRUE 
  ); 
END;  
/
```

### По кількох таблицях:

``` sql
BEGIN
  FOR rec IN (SELECT o .Object_Name , o .Owner
                FROM dba_objects o
               WHERE o. Object_Name IN ('BUSINESSPARTNER_BRANCH', 'CONTRAGENT','FMA_Contragent' )
                 AND o. Object_Type = 'TABLE' )
  LOOP
    dbms_stats.gather_table_stats( rec.Owner, rec.Object_Name , CASCADE => TRUE);
  END LOOP;  
END;
/
```