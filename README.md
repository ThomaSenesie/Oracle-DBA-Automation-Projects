
# Oracle-DBA-Automation-Projects
Ansible playbooks for automating Oracle 19c non-CDB database administration, including environment setup, software installation, database creation, patching, compliance, monitoring, and schema management. Built for VirtualBox VMs, these playbooks demonstrate Oracle DBA expertise and advanced automation for enterprise-grade database operations.

# Oracle DBA Automation Projects

Ansible playbooks for automating Oracle 19c non-CDB database administration, including environment setup, software installation, database creation, patching, compliance, monitoring, and schema management. Built for VirtualBox VMs with Oracle Linux 8, these playbooks demonstrate Oracle DBA expertise and advanced automation for enterprise-grade database operations.

## Overview

This repository contains a suite of Ansible playbooks to streamline Oracle 19c non-CDB database management. Each playbook is documented in its own `README.md` within the `playbooks/` directory, detailing its purpose, features, and usage. The flagship project, `install_oracle.yml`, sets up the entire Oracle environment, while other playbooks handle specific DBA tasks.

## Playbooks

All playbooks are stored in the `playbooks/` directory, with individual `README.md` files for detailed documentation:

- **[install_oracle.yml](playbooks/install_oracle.yml)**: Sets up Oracle 19c environment, installs software, and creates databases with sample schemas.
- **[patch_oracle.yml](playbooks/patch_oracle.yml)**: Applies Oracle 19c Combo Patch 32545008.
- **[compliance_oracle.yml](playbooks/compliance_oracle.yml)**: Ensures security compliance (CIS/STIG).
- **[monitor_oracle.yml](playbooks/monitor_oracle.yml)**: Monitors database health (tablespaces, alert logs, waits, blocks).
- **[manage_users.yml](playbooks/manage_users.yml)**: Manages database users and privileges.
- **[transfer_schema.yml](playbooks/transfer_schema.yml)**: Transfers schemas using Data Pump.
- **[backup_oracle.yml](playbooks/backup_oracle.yml)**: Automates RMAN backups.
- **[manage_tablespace.yml](playbooks/manage_tablespace.yml)**: Manages tablespace configurations.
- **[schedule_backup.yml](playbooks/schedule_backup.yml)**: Schedules recurring backups.
- **[detect_blocking_sessions_only.yml](playbooks/detect_blocking_sessions_only.yml)**, **[detect_wait_events.yml](playbooks/detect_wait_events.yml)**: Monitors blocking sessions and wait events.
- **[generate_awr.yml](playbooks/generate_awr.yml)**: Generates AWR reports.

## Repository Structure

```
Oracle-DBA-Automation-Projects/
├── inventory                  # Host inventory
├── playbooks/                 # Playbooks and their READMEs
│   ├── install_oracle.yml     # Environment setup and DB creation
│   ├── README_install_oracle.md  # Documentation for install_oracle.yml
│   ├── patch_oracle.yml       # Patching
│   ├── README_patch_oracle.md # Documentation for patch_oracle.yml
│   ├── compliance_oracle.yml  # Compliance
│   ├── README_compliance_oracle.md  # Documentation for compliance_oracle.yml
│   ├── monitor_oracle.yml     # Monitoring
│   ├── README_monitor_oracle.md  # Documentation for monitor_oracle.yml
│   ├── manage_users.yml       # User management
│   ├── README_manage_users.md # Documentation for manage_users.yml
│   ├── transfer_schema.yml    # Schema transfer
│   ├── README_transfer_schema.md  # Documentation for transfer_schema.yml
│   ├── backup_oracle.yml      # Backup automation
│   ├── README_backup_oracle.md  # Documentation for backup_oracle.yml
│   ├── manage_tablespace.yml  # Tablespace management
│   ├── README_manage_tablespace.md  # Documentation for manage_tablespace.yml
│   ├── schedule_backup.yml    # Backup scheduling
│   ├── README_schedule_backup.md  # Documentation for schedule_backup.yml
│   ├── detect_blocking_sessions_only.yml  # Blocking session detection
│   ├── README_detect_blocking_sessions_only.md  # Documentation
│   ├── detect_wait_events.yml # Wait event detection
│   ├── README_detect_wait_events.md  # Documentation
│   ├── generate_awr.yml       # AWR report generation
│   ├── README_generate_awr.md # Documentation for generate_awr.yml
├── files/                     # Oracle binaries (e.g., LINUX.X64_193000_db_home.zip)
├── roles/
│   ├── oracle_install/        # Oracle installation role (tasks, templates, vars)
│   ├── oracle_precheck/       # Shared precheck role
│   ├── oracle_patch/          # Patching role
│   ├── oracle_monitor/        # Monitoring role
│   ├── oracle_compliance/     # Compliance role
│   ├── oracle_user/           # User management role
│   ├── oracle_transfer_schema/ # Schema transfer role
│   ├── oracle_backup/         # Backup role
│   ├── oracle_tablespace/     # Tablespace management role
│   ├── backup_schedulePure/   # Backup scheduling role
│   ├── oracle_awr/            # AWR report role
├── ansible.cfg                # Ansible configuration
├── README.md                  # Project overview
├── .gitignore                 # Ignored files
└── LICENSE                    # MIT License
```

## Prerequisites
- Oracle 19c non-CDB database
- Ansible 2.9+
- VirtualBox 7.0, Oracle Linux 8
- Oracle 19c binaries (`LINUX.X64_193000_db_home.zip`), patch files
- Host: 16GB RAM, 100GB disk, 4+ core CPU

## Environment
- **Servers**: `ansible-server` (192.168.56.100), `prod-server` (192.168.56.101, SID: PROD), `test-server` (192.168.56.102, SID: TEST)
- **Oracle Home**: `/u01/app/oracle/product/19.3.0/dbhome_1`
- **Backup Directory**: `/u02/backup`
- **Network**: Host-only or bridged adapter

## Author
**Thomas Senesie**  
Oracle DBA with expertise in Ansible automation.  
- Email: senesiethomas02@gmail.com  
- GitHub: [ThomaSenesie](https://github.com/ThomaSenesie)

## License
MIT License - see [LICENSE](LICENSE).
(Add install_oracle.yml, oracle_install role, and READMEs for Oracle 19c environment setup)
