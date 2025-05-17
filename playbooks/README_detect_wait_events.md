# detect_wait_events.yml

## Purpose
Automates the detection of non-idle wait events in an Oracle 19c non-CDB database (PROD), providing insights into performance bottlenecks for proactive DBA monitoring. This playbook generates detailed wait event reports for enterprise Oracle automation.

## Features
- **Wait Event Detection**: Queries v\$session for non-idle wait events in WAITING state, including SID, event, wait time, seconds in wait, and associated SQL text.
- **Output Display**: Outputs wait event details directly to the console for immediate analysis.
- **Environment Setup**: Configures Oracle environment variables for seamless SQL*Plus execution.

## Tasks
- Queries v\$session and v\$sql to retrieve wait event details for PROD database.
- Displays results using debug module for visibility.
- Fails on SQL*Plus errors or non-zero exit codes.

## Variables
- oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
- db_sid: PROD

## Usage
```bash
ansible-playbook playbooks/detect_wait_events.yml -i inventory
```

## Verification
- **Check Wait Events**:
  ```bash
  ssh oracle@192.168.56.101
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  export ORACLE_SID=PROD
  sqlplus / as sysdba
  SET LINESIZE 200
  SET PAGESIZE 100
  SELECT s.sid, s.event, s.wait_time, s.seconds_in_wait, sql.sql_text
  FROM v\$session s
  LEFT JOIN v\$sql sql ON s.sql_id = sql.sql_id
  WHERE s.wait_class != 'Idle' AND s.state = 'WAITING';
  EXIT;
  ```
  - Expected: Lists active wait events with details or no rows if none exist.
- **Playbook Output**: Review console output for wait_result.stdout_lines, showing SID, event, wait time, and SQL text.

## Notes
- Targets PROD database only (prod-server, 192.168.56.101).
- No dedicated role; tasks are defined directly in the playbook.
- Requires sufficient permissions for oracle user to query v\$session and v\$sql.
