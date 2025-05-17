# patch_oracle.yml

## Purpose
Automates the application of Oracle 19c Combo Patch 32545008 (RU 19.12.0.0.210720) to a non-CDB database, ensuring stability and security. This playbook streamlines patching, backups, and verification, showcasing enterprise-grade Oracle DBA automation.

## Features
- **Patch Application**: Applies sub-patches 32545013 (Database RU) and 32579761 (OCW RU) to Oracle 19c in `/u01/app/oracle/product/19.3.0/dbhome_1`.
- **Backups**: Creates Oracle home tarball and RMAN Level 0 database backup in `/u02/backup`.
- **OPatch Upgrade**: Updates OPatch to 12.2.0.1.25 for compatibility.
- **Prechecks**: Validates disk space (15GB+ in `/u01`, `/u02`), Oracle inventory, and patch conflicts.
- **Post-Patch**: Runs `datapatch` and `utlrp.sql`, restarts database and listener, verifies patch via `opatch lspatches`.
- **Troubleshooting**: Handles issues like backup path mismatches, space check failures, and SQL patch registry updates.

## Role
- **oracle_patch**
  - **Tasks**:
    - Checks disk space and creates directories (`/u01/patches`, `/u02/backup/oracle_home`).
    - Copies and unzips patch files (`p32545008_190000_Linux-x86-64.zip`, `p6880880_190000_Linux-x86-64.zip`) from `files/`.
    - Backs up Oracle home and validates tarball.
    - Performs RMAN Level 0 backup if none exists.
    - Stops listener and database, upgrades OPatch, applies sub-patches.
    - Restarts database/listener, runs `datapatch` and `utlrp.sql`, verifies patch application.
  - **Variables** (in `vars/main.yml`):
    - `oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1`
    - `patch_dir: /u01/patches`
    - `backup_dir: /u02/backup`
    - `patch_id: 32545008`
    - `sub_patches: [32545013, 32579761]`

## Usage
```bash
ansible-playbook playbooks/patch_oracle.yml -i inventory --limit test
```

## Verification
- **Patch Inventory**:
  ```bash
  ssh oracle@192.168.56.102
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  $ORACLE_HOME/OPatch/opatch lspatches
  ```
  - Expected: Lists 32545013, 32579761.
- **SQL Patch Registry**:
  ```sql
  sqlplus / as sysdba
  SELECT patch_id, status FROM dba_registry_sqlpatch WHERE patch_id IN (32545013, 32579761);
  ```
  - Expected: 32545013 (SUCCESS), 32579761 (post-datapatch).
- **Database Status**:
  ```sql
  SELECT instance_name, status, version FROM v$instance;
  ```
  - Expected: TEST, OPEN, 19.0.0.0.0.
- **Logs**: `/u02/backup/playbook_patch_test.log`

## Notes
- Requires patch files in `files/`.
- Tested on `test-server` (SID: TEST) before `prod-server`.
- Backup retention: Oracle home and RMAN backups in `/u02/backup`.
