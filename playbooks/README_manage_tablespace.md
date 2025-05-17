# manage_tablespace.yml

## Purpose
Automates the creation, resizing, and monitoring of tablespaces in Oracle 19c non-CDB databases, ensuring efficient storage management and proactive usage alerts. This playbook streamlines tablespace operations for enterprise Oracle DBA automation.

## Features
- **Tablespace Creation**: Creates a new tablespace (APP_DATA) with a datafile, autoextend enabled (5M increments, 100M max).
- **Tablespace Resizing**: Resizes the USERS tablespace datafile to a specified size.
- **Usage Monitoring**: Monitors tablespace usage, alerting when usage exceeds 80%.
- **Prechecks**: Uses oracle_precheck role to validate disk space, memory, database, and listener status.
- **Disk Space Check**: Ensures at least 5GB free in /u02 before operations.

## Role
- **oracle_tablespace**
  - **Tasks**:
    - Checks disk space using df -h for /u02 (>5GB free).
    - Creates tablespace APP_DATA with datafile at /u02/oradata/{{ db_sid }}/app_data01.dbf.
    - Resizes USERS tablespace datafile at /u02/oradata/{{ db_sid }}/users01.dbf.
    - Monitors tablespace usage via dba_tablespace_usage_metrics, alerting for >80% usage.
  - **Variables** (in vars/main.yml):
    - oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
    - tablespace_name: APP_DATA
    - datafile_path: /u02/oradata/{{ db_sid }}/app_data01.dbf
    - tablespace_size: 20M
    - users_datafile: /u02/oradata/{{ db_sid }}/users01.dbf
    - resize_size: 20M
    - db_sid: PROD (for prod-server), TEST (for test-server)

## Usage
```bash
ansible-playbook playbooks/manage_tablespace.yml -i inventory --limit test
```

## Verification
- **Check Tablespaces**:
  ```bash
  ssh oracle@192.168.56.101
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  export ORACLE_SID=PROD
  sqlplus / as sysdba
  SELECT tablespace_name, file_name, bytes/1024/1024 AS size_mb FROM dba_data_files WHERE tablespace_name IN ('APP_DATA', 'USERS');
  SELECT tablespace_name, ROUND((used_space/total_space)*100) AS usage_percent FROM dba_tablespace_usage_metrics;
  EXIT;
  ```
  - Repeat for test-server (192.168.56.102, TEST).
- **Verify APP_DATA**:
  ```bash
  sqlplus / as sysdba
  SELECT tablespace_name FROM dba_tablespaces WHERE tablespace_name = 'APP_DATA';
  EXIT;
  ```
  - Expected: APP_DATA listed.

## Notes
- Requires oracle_precheck role for system validation.
- Datafiles stored in /u02/oradata/{{ db_sid }}/ (ensure >5GB free space).
- Supports PROD and TEST SIDs.
