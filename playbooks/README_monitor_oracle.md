# monitor_oracle.yml

## Purpose
Automates monitoring of Oracle 19c non-CDB databases, checking listener and database status, tablespace usage, alert log errors, wait events, and blocking sessions. This playbook generates reports for proactive database health management in enterprise Oracle DBA automation.

## Features
- **Listener Monitoring**: Verifies listener status, starts it if down, and saves status to a report.
- **Database Monitoring**: Checks database status, starts it if not open, and saves status.
- **Tablespace Usage**: Reports tablespace usage with warnings for >85% utilization.
- **Alert Log Errors**: Scans alert log for ORA- errors, reporting findings.
- **Wait Events**: Queries non-idle wait events with SQL text for performance analysis.
- **Blocking Sessions**: Identifies blocking sessions with lock details and SQL text.
- **Prechecks**: Uses oracle_precheck role to validate system readiness.
- **Report Generation**: Saves reports to /u02/backup/monitor/ with timestamps.

## Role
- **oracle_monitor**
  - **Tasks**:
    - Creates monitoring report directory (/u02/backup/monitor).
    - Checks and starts listener, saving status to listener_status_{{ db_sid }}_YYYYMMDD.txt.
    - Checks and starts database, saving status to db_status_{{ db_sid }}_YYYYMMDD.txt.
    - Reports tablespace usage to tablespace_{{ db_sid }}_YYYYMMDD.txt with high-usage warnings.
    - Scans alert log for ORA- errors, saving to alert_log_{{ db_sid }}_YYYYMMDD.txt.
    - Queries wait events, saving to waits_{{ db_sid }}_YYYYMMDD.txt.
    - Identifies blocking sessions, saving to blocks_{{ db_sid }}_YYYYMMDD.txt.
  - **Variables** (in vars/main.yml):
    - oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
    - db_sid: PROD (for prod-server), TEST (for test-server)

## Usage
```bash
ansible-playbook playbooks/monitor_oracle.yml -i inventory --limit test
```

## Verification
- **Check Reports**:
  ```bash
  ssh oracle@192.168.56.102
  ls -l /u02/backup/monitor/*TEST_*
  cat /u02/backup/monitor/listener_status_TEST_YYYYMMDD.txt
  cat /u02/backup/monitor/db_status_TEST_YYYYMMDD.txt
  cat /u02/backup/monitor/tablespace_TEST_YYYYMMDD.txt
  cat /u02/backup/monitor/alert_log_TEST_YYYYMMDD.txt
  cat /u02/backup/monitor/waits_TEST_YYYYMMDD.txt
  cat /u02/backup/monitor/blocks_TEST_YYYYMMDD.txt
  ```
  - Repeat for prod-server (192.168.56.101, PROD).
  - Expected:
    - Listener: “The command completed successfully” or start confirmation.
    - Database: “OPEN” status or start confirmation.
    - Tablespace: Usage details with warnings for >85%.
    - Alert Log: “No errors” or ORA- errors listed.
    - Wait Events: Lists non-idle waits or “No wait events found”.
    - Blocking Sessions: Lists blocking sessions or “No blocking sessions found”.

## Notes
- Requires oracle_precheck role for system validation.
- Reports stored in /u02/backup/monitor/ (ensure sufficient space).
- Supports PROD and TEST SIDs.
