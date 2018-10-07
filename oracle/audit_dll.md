---
layout: default
title: Audit on DDL changes on Oracle RDBMS
---
# Add audit on DDL changes on Oracle RDBMS

If your database is in noarchive mode for audit DDL change you can create trigger:

``` sql
DROP TRIGGER ddl_trigger;
DROP TABLE Audit_Log;

CREATE TABLE Audit_Log (
  OsDate      DATE,
  Operation   VARCHAR2 (10),
  Name        VARCHAR2 (255),
  Extra       VARCHAR2 (4000),
  Owner       VARCHAR2 (25),
  SID         NUMBER(10),
  AudSid      NUMBER(10),
  Serial      NUMBER(10),
  OsUser      VARCHAR2(30),
  Machine     VARCHAR2(64),
  Terminal    VARCHAR2(30),
  PROGRAM     VARCHAR2(48),
  Module      VARCHAR2(48)
  );


CREATE OR REPLACE TRIGGER ddl_Trigger
  AFTER CREATE OR ALTER OR DROP 
  ON SCHEMA
DECLARE
  l_sysevent   VARCHAR2 (25);
  
  lSid      NUMBER;
  lAudSid   NUMBER;
  lSerial   NUMBER;
  lOsUser   VARCHAR2(30);
  lMachine  VARCHAR2(64);
  lTerminal VARCHAR2(30);
  lProgram  VARCHAR2(48);
  lModule   VARCHAR2(48);
  
BEGIN
  SELECT ora_sysevent INTO l_sysevent FROM DUAL;
  
  SELECT SID,
         AudSid,
         Serial#,
         SUBSTR(NVL(UPPER(TRIM(s.OSUser)), '<unknown>'), 1, 30) AS OSUser,
         SUBSTR(NVL(UPPER(TRIM(s.machine)), '<unknown>'), 1, 64) AS Machine,
         SUBSTR(NVL(UPPER(TRIM(s.Terminal)), '<unknown>'), 1, 30) AS Terminal,
         SUBSTR(NVL(UPPER(TRIM(s.PROGRAM)), '<unknown>'), 1, 48) AS PROGRAM,
         SUBSTR(NVL(UPPER(TRIM(s.module)), '<unknown>'), 1, 48) AS Module
    INTO lSid,
         lAudSid,
         lSerial,
         lOsUser,
         lMachine,
         lTerminal,
         lProgram,
         lModule
    FROM vsession s
   WHERE s.audsid = USERENV('SESSIONID')
     AND s.SID = (SELECT m.SID
                    FROM v$mystat m
                   WHERE ROWNUM = 1)
     AND ROWNUM = 1;  

  IF (l_sysevent IN ('DROP', 'CREATE')) THEN
    INSERT INTO Audit_Log
      SELECT SYSDATE,
             ora_sysevent,
             ora_dict_obj_name,
             NULL,
             ora_dict_obj_owner,
             lSid,
             lAudSid,
             lSerial,             
             lOsUser,               
             lMachine,              
             lTerminal,             
             lProgram,              
             lModule
        FROM DUAL;
  ELSIF (l_sysevent = 'ALTER') THEN
    INSERT INTO Audit_Log
      SELECT SYSDATE,
             ora_sysevent,
             ora_dict_obj_name,
             sql_text,
             ora_dict_obj_owner,
             lSid,
             lAudSid,
             lSerial, 
             lOsUser,               
             lMachine,              
             lTerminal,             
             lProgram,              
             lModule
        FROM v$open_cursor
       WHERE UPPER (
               sql_text) LIKE
               'ALTER%';
  END IF;
END;
/

/*
Test audit
*/
DROP TABLE test_audit_table
/
CREATE TABLE test_audit_table (x INT)
/
ALTER TABLE test_audit_table
  ADD y INT
/ 
CREATE OR REPLACE TRIGGER tr_test_audit_table
AFTER UPDATE ON test_audit_table
BEGIN
  NULL;
END;
/   
ALTER TRIGGER tr_test_audit_table DISABLE
/
ALTER TRIGGER tr_test_audit_table ENABLE
/
CREATE OR REPLACE PROCEDURE pr_test_audit_table
IS
  i NUMBER;
BEGIN
  NULL;
END;
/
DROP TABLE test_audit_table
/
DROP PROCEDURE pr_test_audit_table
/
/*
format sqlplus output
*/
SET linesize 5000
COLUMN extra format a50
COLUMN owner format a15
COLUMN osuser format a15
COLUMN machine format a20
COLUMN terminal format a20
COLUMN PROGRAM format a20

SELECT * FROM Audit_Log ORDER BY OsDate DESC
/
```