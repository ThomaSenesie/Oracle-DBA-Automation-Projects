# oracle_precheck Role

## Purpose
Automates precheck validations for Oracle 19c non-CDB databases to ensure system readiness before executing critical DBA tasks like installation, patching, or backups. This role enhances reliability in enterprise Oracle automation workflows.

## Features
- **Disk Space Check**: Verifies at least 5GB free space in /u01 and /u02 directories.
- **Memory Check**: Ensures at least 20MB of free memory available.
- **Database Status**: Confirms the database is in OPEN status using SQL*Plus.
- **Listener Status**: Validates the Oracle listener is running and responsive.

## Role
- **oracle_precheck**
  - **Tasks**:
    - Checks disk space using df -h for /u01 and /u02.
    - Verifies free memory using free -m.
    - Queries v\$instance to confirm database status (OPEN).
    - Runs lsnrctl status to verify listener functionality.
  - **Variables** (used in playbooks):
    - oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
    - db_sid: PROD (for prod-server), TEST (for test-server)

## Usage
Included as a prerequisite role in playbooks (e.g., install_oracle.yml, patch_oracle.yml, backup_oracle.yml):
```bash
ansible-playbook playbooks/<playbook>.yml -i inventory --limit test
```

## Verification
- **Disk Space**:
  ```bash
  df -h /u01 /u02
  ```
  - Expected: >5GB free in each.
- **Memory**:
  ```bash
  free -m
  ```
  - Expected: >20MB free.
- **Database Status**:
  ```bash
  ssh oracle@192.168.56.101
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=\$ORACLE_HOME/bin:\$PATH
  export ORACLE_SID=PROD
  sqlplus / as sysdba
  SELECT status FROM v\$instance;
  EXIT;
  ```
  - Expected: OPEN.
- **Listener Status**:
  ```bash
  \$ORACLE_HOME/bin/lsnrctl status
  ```
  - Expected: “The command completed successfully”.

## Notes
- Used as a dependency in other playbooks to ensure system readiness.
- Supports PROD and TEST SIDs.
- Fails tasks if precheck conditions are not met, preventing unsafe operations.
