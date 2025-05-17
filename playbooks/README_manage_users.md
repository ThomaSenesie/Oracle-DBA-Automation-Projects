# manage_users.yml

## Purpose
Automates the creation and configuration of database users in Oracle 19c non-CDB databases, ensuring secure and efficient user management. This playbook streamlines user setup for enterprise Oracle DBA automation.

## Features
- **User Creation**: Creates the APP_USER database user with a specified password, default tablespace, and temporary tablespace.
- **Privilege Assignment**: Grants CREATE SESSION, CREATE TABLE, RESOURCE, and CONNECT privileges to APP_USER.
- **Quota Management**: Assigns a 100MB quota on the APP_DATA tablespace for APP_USER.
- **Prechecks**: Uses oracle_precheck role to validate disk space, memory, database, and listener status before user creation.

## Role
- **oracle_user**
  - **Tasks**:
    - Creates APP_USER with password AppPass123, assigned to APP_DATA tablespace and TEMP temporary tablespace.
    - Grants CREATE SESSION, CREATE TABLE, RESOURCE, and CONNECT privileges.
    - Sets 100MB quota on APP_DATA tablespace.
  - **Variables** (in manage_users.yml):
    - oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
    - db_sid: PROD (for prod-server), TEST (for test-server)

## Usage
```bash
ansible-playbook playbooks/manage_users.yml -i inventory --limit test
```

## Verification
- **Check User Details**:
  ```bash
  ssh oracle@192.168.56.101
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  export ORACLE_SID=PROD
  sqlplus / as sysdba
  SELECT username, default_tablespace, temporary_tablespace FROM dba_users WHERE username = 'APP_USER';
  SELECT privilege FROM dba_sys_privs WHERE grantee = 'APP_USER';
  SELECT tablespace_name, bytes/1024/1024 AS quota_mb FROM dba_ts_quotas WHERE username = 'APP_USER';
  EXIT;
  ```
  - Repeat for test-server (192.168.56.102, TEST).
- **Test User Login**:
  ```bash
  sqlplus app_user/AppPass123
  SELECT sysdate FROM dual;
  EXIT;
  ```
  - Expected: Successful login, query returns current date.

## Notes
- Requires oracle_precheck role for system validation and APP_DATA/TEMP tablespaces.
- Supports PROD and TEST SIDs.
- Logs stored in /home/oracle/ansible/playbook_users.log.
