# backup_oracle.yml

## Purpose
Automates the configuration and execution of RMAN incremental level 0 database and archive log backups for Oracle 19c non-CDB databases, ensuring data protection and recovery readiness. This playbook streamlines backup processes, ideal for enterprise Oracle DBA automation.

## Features
- **Backup Execution**: Performs RMAN incremental level 0 database backups and archive log backups, storing them in `/u02/backup/rman`.
- **Directory Setup**: Creates `/u02/backup/rman` with appropriate permissions.
- **RMAN Script**: Generates and executes an RMAN script for full database and archive log backups, deleting obsolete backups.
- **Prechecks**: Uses `oracle_precheck` role to validate disk space, memory, database, and listener status.
- **Validation**: Verifies backup integrity via RMAN `list backup` and optional validation commands.

## Role
- **oracle_backup**
  - **Tasks**:
    - Creates backup directory (`/u02/backup/rman`).
    - Configures RMAN script (`rman_backup.rman`) for incremental level 0 and archive log backups.
    - Executes RMAN backup, failing on errors.
    - Validates backup by listing database backups in RMAN.
  - **Variables** (in `backup_oracle.yml`):
    - `db_sid: PROD` (for `prod-server`), `TEST` (for `test-server`)
    - `oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1`

## Usage
```bash
ansible-playbook playbooks/backup_oracle.yml -i inventory --limit test
```

## Verification
- **Check Backup Files**:
  ```bash
  ssh oracle@192.168.56.101
  ls -l /u02/backup/rman/full_PROD_*.bak
  ls -l /u02/backup/rman/arch_PROD_*.bak
  ```
  - Repeat for `test-server` (192.168.56.102, `TEST`).
- **RMAN Validation**:
  ```bash
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  export ORACLE_SID=PROD
  rman target /
  LIST BACKUP OF DATABASE;
  EXIT;
  ```
  - Expected: Lists backup sets.
- **Integrity Check**:
  ```bash
  rman target /
  VALIDATE DATABASE;
  RESTORE DATABASE VALIDATE;
  EXIT;
  ```
  - Expected: No corruption reported.
- **Logs**: `/home/oracle/ansible/playbook_backup.log`

## Notes
- Requires `oracle_precheck` role for system validation.
- Backup files stored in `/u02/backup/rman` (ensure >5GB free space).
- Supports `PROD` and `TEST` SIDs.
