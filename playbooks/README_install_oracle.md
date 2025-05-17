# install_oracle.yml

## Purpose
Automates the deployment of an Oracle 19c non-CDB environment across VirtualBox VMs, streamlining Oracle software installation, database creation, and configuration for production and test databases. This playbook ensures a consistent, repeatable setup for Oracle DBAs, ideal for enterprise environments requiring high reliability and security.

## Features
- **VM Configuration**: Sets up three Oracle Linux 8 VMs:
  - `ansible-server` (192.168.56.100): Ansible control node.
  - `prod-server` (192.168.56.101, SID: PROD): Production database.
  - `test-server` (192.168.56.102, SID: TEST): Test database.
- **Oracle Installation**: Installs Oracle 19c Enterprise Edition in `/u01/app/oracle/product/19.3.0/dbhome_1` using silent installation.
- **Database Creation**: Creates non-CDB databases with sample schemas (`HR`, `OE`, `PM`, `IX`, `SH`) in `/u02/oradata`, enabling `ARCHIVELOG` mode for recovery.
- **Network and Listener Setup**: Configures listener on port 1521 with `listener.ora` and `tnsnames.ora` for connectivity.
- **Security**: Locks sample schema users (`HR`, `OE`, `PM`, `IX`, `SH`) to prevent unauthorized access.
- **Fast Recovery Area**: Configures `/u02/oradata` as the Fast Recovery Area with a 10GB limit.

## Role
- **oracle_install**
  - **Tasks**:
    - Copies Oracle binaries (`LINUX.X64_193000_db_home.zip`) from `files/` to `/tmp`.
    - Extracts binaries to `/u01/app/oracle/product/19.3.0/dbhome_1`.
    - Runs silent installation with `runInstaller` using `install.rsp.j2`.
    - Executes `orainstRoot.sh` and `root.sh` for system configuration.
    - Creates database using `dbca` with `dbca.rsp.j2`, enabling sample schemas.
    - Sets database parameters (e.g., `processes=100`) via SQL*Plus.
    - Enables `ARCHIVELOG` mode and configures Fast Recovery Area.
    - Deploys `listener.ora` and `tnsnames.ora` templates and starts the listener.
  - **Templates**:
    - `install.rsp.j2`: Configures silent installation (e.g., `ORACLE_HOME`, `UNIX_GROUP_NAME=oinstall`).
    - `dbca.rsp.j2`: Defines database creation parameters (e.g., `sid=PROD/TEST`, `sampleSchema=true`).
    - `listener.ora.j2`: Sets listener configuration (TCP, port 1521).
    - `tnsnames.ora.j2`: Configures TNS entries for database connectivity.
  - **Variables** (in `vars/main.yml`):
    - `oracle_user: oracle`
    - `oracle_group: oinstall`
    - `oracle_base: /u01/app/oracle`
    - `oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1`
    - `ora_inventory: /u01/app/oraInventory`
    - `oracle_edition: EE`
    - `sys_password: Oracle123`
    - `init_params.processes: 100`

## Usage
```bash
ansible-playbook playbooks/install_oracle.yml -i inventory --limit test
```

## Verification
- **Check Installation**:
  ```bash
  ssh oracle@192.168.56.101
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  export ORACLE_SID=PROD
  sqlplus / as sysdba
  SELECT instance_name FROM v\$instance;
  SELECT username FROM dba_users WHERE username IN ('HR', 'OE', 'PM', 'IX', 'SH');
  SELECT table_name FROM dba_tables WHERE owner = 'HR';
  EXIT;
  ```
- **Logs**:
  - DBCA logs: `/u01/app/oracle/cfgtoollogs/dbca/PROD/*.log`
  - Installer logs: `/u01/app/oraInventory/logs/InstallActions*/installActions*.log`

## Security Considerations
- Sample schema users are locked to prevent unauthorized access.
- Passwords (`sys_password=Oracle123`) should be updated in production.
- Host-only network (192.168.56.0/24) minimizes WiFi leaks.

## Cleanup
To remove the setup:
```bash
rm -rf /tmp/*
rm -rf /u02/oradata/*
rm -rf /u01/app/oracle/cfgtoollogs/dbca/*
rm -rf /u01/app/oracle/diag/rdbms/*
rm -rf /u01/app/oracle/oradata/*
rm -rf /u01/app/oraInventory/*
rm -rf /etc/oratab
rm -rf /etc/oraInst.loc
rm -f /u01/app/oracle/product/19.3.0/dbhome_1/network/admin/listener.ora
rm -f /u01/app/oracle/product/19.3.0/dbhome_1/network/admin/tnsnames.ora
```

## Notes
- Requires Oracle 19c binaries in `files/LINUX.X64_193000_db_home.zip`.
- Sample schemas add ~100-200 MB to `/u02/oradata`.
- Playbook supports both `PROD` and `TEST` SIDs with identical configurations.
