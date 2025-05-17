# compliance_oracle.yml

## Purpose
Automates security compliance checks for Oracle 19c non-CDB databases, generating reports on password policies, audit trails, privileged users, system privileges, and object privileges to ensure adherence to CIS/STIG standards. This playbook enhances enterprise Oracle DBA security automation.

## Features
- **Password Policy Check**: Reports on password settings (e.g., PASSWORD_LIFE_TIME, FAILED_LOGIN_ATTEMPTS) from dba_profiles.
- **Audit Trail Check**: Verifies audit parameters (e.g., audit_trail, audit_file_dest) from v\$parameter.
- **Privileged User Check**: Lists users with DBA, SYSDBA, SYSOPER, or APPLICATION roles from dba_role_privs and dba_users.
- **Privilege Checks**: Reports system (dba_sys_privs) and object (dba_tab_privs) privileges for privileged and application users.
- **Report Generation**: Saves formatted reports to /u02/backup/compliance/ with timestamps.
- **Prechecks**: Uses oracle_precheck role to validate disk space, memory, database, and listener status.

## Role
- **oracle_compliance**
  - **Tasks**:
    - Creates report directory (/u02/backup/compliance).
    - Checks password policies, saving to password_policy_{{ db_sid }}_YYYYMMDD.txt.
    - Verifies audit trail settings, saving to audit_trail_{{ db_sid }}_YYYYMMDD.txt.
    - Lists privileged and application users, saving to privileged_users_{{ db_sid }}_YYYYMMDD.txt.
    - Reports system privileges, saving to system_privileges_{{ db_sid }}_YYYYMMDD.txt.
    - Reports object privileges, saving to object_privileges_{{ db_sid }}_YYYYMMDD.txt.
  - **Variables** (in vars/main.yml):
    - oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
    - db_sid: PROD (for prod-server), TEST (for test-server)

## Usage
```bash
ansible-playbook playbooks/compliance_oracle.yml -i inventory --limit test
```

## Verification
- **Check Reports**:
  ```bash
  ssh oracle@192.168.56.101
  ls -l /u02/backup/compliance/*PROD_*
  cat /u02/backup/compliance/password_policy_PROD_YYYYMMDD.txt
  cat /u02/backup/compliance/audit_trail_PROD_YYYYMMDD.txt
  cat /u02/backup/compliance/privileged_users_PROD_YYYYMMDD.txt
  cat /u02/backup/compliance/system_privileges_PROD_YYYYMMDD.txt
  cat /u02/backup/compliance/object_privileges_PROD_YYYYMMDD.txt
  ```
  - Repeat for test-server (192.168.56.102, TEST).
  - Expected:
    - Password Policy: Lists limits (e.g., FAILED_LOGIN_ATTEMPTS 10, PASSWORD_LIFE_TIME 180).
    - Audit Trail: Shows audit settings (e.g., audit_trail DB).
    - Privileged Users: Includes SYS, SYSTEM, APP_USER (APPLICATION role).
    - System/Object Privileges: Lists privileges for relevant users.
- **Logs**: /home/oracle/ansible/compliance_test.log

## Notes
- Requires oracle_precheck role for system validation.
- Reports stored in /u02/backup/compliance/ (ensure sufficient space).
- Supports PROD and TEST SIDs.
