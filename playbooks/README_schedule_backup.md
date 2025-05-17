# schedule_backup.yml

## Purpose
Automates the scheduling of RMAN incremental backups for Oracle 19c non-CDB databases using cron jobs, ensuring regular data protection with minimal manual intervention. This playbook sets up weekly full and daily incremental backups, enhancing enterprise Oracle DBA automation.

## Features
- **Backup Scheduling**: Configures cron jobs for:
  - Weekly full RMAN incremental level 0 backups (Sundays, 00:00).
  - Daily incremental level 1 backups (Monday-Saturday, 02:00).
- **Script Deployment**: Deploys RMAN backup scripts (`rman_full_backup.sh`, `rman_incremental_backup.sh`) to `/u02/scripts`.
- **Directory Setup**: Creates `/u02/backup/rman` and `/u02/scripts` with appropriate permissions.
- **Backup Configuration**: Sets RMAN retention policy (redundancy 2), enables control file autobackup, and deletes obsolete backups.
- **Multi-Channel Backup**: Uses two RMAN channels for parallel processing, storing backups in `/u02/backup/rman`.

## Role
- **backup_schedulePure**
  - **Tasks**:
    - Creates directories (`/u02/backup/rman`, `/u02/scripts`).
    - Deploys RMAN full and incremental backup scripts with retention and autobackup settings.
    - Schedules cron jobs for weekly full and daily incremental backups, logging to `/u02/backup/rman/`.
  - **Variables** (in `schedule_backup.yml`):
    - `oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1`
    - `backup_dir: /u02/backup/rman`
    - `db_sid: PROD` (for `prod-server`), `TEST` (for `test-server`)

## Usage
```bash
ansible-playbook playbooks/schedule_backup.yml -i inventory
```

## Verification
- **Check Scripts**:
  ```bash
  ssh oracle@192.168.56.101
  ls -l /u02/scripts/rman_full_backup.sh /u02/scripts/rman_incremental_backup.sh
  ```
  - Repeat for `test-server` (192.168.56.102).
- **Check Cron Jobs**:
  ```bash
  crontab -u oracle -l
  ```
  - Expected: Lists weekly (Sunday, 00:00) and daily (Monday-Saturday, 02:00) backups.
- **Test Backup Manually**:
  ```bash
  /u02/scripts/rman_full_backup.sh
  cat /u02/backup/rman/rman_full_backup_$(date +%Y%m%d).log
  ```
  - Expected: Backup completes, log shows “backup set complete”.
- **Check Backup Files**:
  ```bash
  ls -l /u02/backup/rman/full_*
  ls -l /u02/backup/rman/incr_*
  ```

## Notes
- Backups stored in `/u02/backup/rman` (ensure >5GB free space).
- Supports `PROD` and `TEST` SIDs.
- Logs stored in `/u02/backup/rman/rman_*.log`.
