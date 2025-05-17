# Oracle DBA Automation Projects

Ansible playbooks for automating Oracle 19c non-CDB database administration, including environment setup, software installation, database creation, patching, compliance, monitoring, and schema management. Built for VirtualBox VMs with Oracle Linux 8, these playbooks demonstrate Oracle DBA expertise and advanced automation for enterprise-grade database operations.

## Overview

This repository contains a suite of Ansible playbooks to streamline Oracle 19c non-CDB database management. Each playbook is documented in its own `README.md` within the `playbooks/` directory, detailing its purpose, features, and usage. The flagship project, `install_oracle.yml`, sets up the entire Oracle environment, while other playbooks handle specific DBA tasks.

## Environment Setup

The environment is configured across three VirtualBox VMs on Oracle Linux 8 with a host-only network to prevent WiFi leaks. Key setup steps include:

- **Virtual Machines**:
  - ansible-server: Ansible control node (IP: 192.168.56.100, Hostname: ansible-server.localdomain, 3GB RAM, 40GB disk, 2 CPUs).
  - prod-server: Production database (IP: 192.168.56.101, SID: PROD, Hostname: prod-server.localdomain, 4GB RAM, 50GB disk, 2 CPUs).
  - test-server: Test database (IP: 192.168.56.102, SID: TEST, Hostname: test-server.localdomain, 3.7GB RAM, 50GB disk, 2 CPUs).

- **Network**: Host-only adapter (vboxnet0, subnet: 192.168.56.0/24, gateway: 192.168.56.1).

- **Directories**:
  - Oracle Base: /u01/app/oracle
  - Oracle Home: /u01/app/oracle/product/19.3.0/dbhome_1
  - Data Files: /u02/oradata
  - Inventory: /u01/app/oraInventory
  - Ansible: /home/oracle/ansible
  - Software: /home/oracle/ansible/files

- **Credentials**:
  - Root Password: oracle123
  - Oracle User Password: oracle123
  - SYSTEM/SYS Password: Oracle123

- **/etc/hosts** (on all VMs):
  ```
  127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
  ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
  192.168.56.100 ansible-server.localdomain ansible-server
  192.168.56.101 prod-server.localdomain prod-server
  192.168.56.102 test-server.localdomain test-server
  ```

- **Bash Profiles**:
  - On prod-server (~/.bash_profile):
    ```bash
    export ORACLE_BASE=/u01/app/oracle
    export ORACLE_HOME=$ORACLE_BASE/product/19.3.0/dbhome_1
    export PATH=$ORACLE_HOME/bin:$PATH
    export ORACLE_SID=PROD
    export LD_LIBRARY_PATH=$ORACLE_HOME/lib:/usr/lib
    export NLS_LANG=AMERICAN_AMERICA.UTF8
    export NLS_DATE_FORMAT='DD-MM-YYYY:HH24:MI:SS'
    ```
  - On test-server: Similar, with ORACLE_SID=TEST.

- **Ansible Installation** (on ansible-server only):
  - Install Ansible on the control node (ansible-server):
    ```bash
    sudo dnf update -y
    sudo dnf install -y epel-release
    sudo dnf install -y ansible
    ansible --version
    ```
  - Managed nodes (prod-server, test-server) do not require Ansible installation.

- **SSH Key-Based Authentication**:
  - From ansible-server (oracle user) to prod-server and test-server:
    ```bash
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
    ssh-copy-id oracle@192.168.56.101
    ssh-copy-id oracle@192.168.56.102
    ```

- **Ansible Configuration** (ansible.cfg):
  ```
  [defaults]
  remote_user = oracle
  inventory = /home/oracle/ansible/inventory
  roles_path = /home/oracle/ansible/roles
  host_key_checking = False
  log_path = /home/oracle/ansible/ansible.log

  [privilege_escalation]
  become = true
  become_user = root
  become_method = sudo
  ```

- **Inventory** (inventory):
  ```
  [prod]
  prod-server ansible_host=192.168.56.101 ansible_user=oracle db_sid=PROD

  [test]
  test-server ansible_host=192.168.56.102 ansible_user=oracle db_sid=TEST

  [all:vars]
  ansible_connection=ssh
  ansible_ssh_common_args='-o StrictHostKeyChecking=no'
  ```

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
- **[detect_blocking_sessions_only.yml](playbooks/detect_blocking_sessions_only.yml)**: Detects blocking sessions.
- **[detect_wait_events.yml](playbooks/detect_wait_events.yml)**: Detects wait events.
- **[generate_awr.yml](playbooks/generate_awr.yml)**: Generates AWR reports.

## Repository Structure

```
Oracle-DBA-Automation-Projects/
├── ansible.cfg                # Ansible configuration
├── inventory                  # Host inventory
├── playbooks/                 # Playbooks and their READMEs
│   ├── install_oracle.yml     # Environment setup and DB creation
│   ├── README_install_oracle.md
│   ├── patch_oracle.yml       # Patching
│   ├── README_patch_oracle.md
│   ├── compliance_oracle.yml  # Compliance
│   ├── README_compliance_oracle.md
│   ├── monitor_oracle.yml     # Monitoring
│   ├── README_monitor_oracle.md
│   ├── manage_users.yml       # User management
│   ├── README_manage_users.md
│   ├── transfer_schema.yml    # Schema transfer
│   ├── README_transfer_schema.md
│   ├── backup_oracle.yml      # Backup automation
│   ├── README_backup_oracle.md
│   ├── manage_tablespace.yml  # Tablespace management
│   ├── README_manage_tablespace.md
│   ├── schedule_backup.yml    # Backup scheduling
│   ├── README_schedule_backup.md
│   ├── detect_blocking_sessions_only.yml  # Blocking session detection
│   ├── README_detect_blocking_sessions_only.md
│   ├── detect_wait_events.yml # Wait event detection
│   ├── README_detect_wait_events.md
│   ├── generate_awr.yml       # AWR report generation
│   ├── README_generate_awr.md
├── files/                     # Oracle binaries (e.g., LINUX.X64_193000_db_home.zip)
├── roles/
│   ├── oracle_install/        # Oracle installation role
│   ├── oracle_precheck/       # Shared precheck role
│   ├── oracle_patch/          # Patching role
│   ├── oracle_monitor/        # Monitoring role
│   ├── oracle_compliance/     # Compliance role
│   ├── oracle_user/           # User management role
│   ├── oracle_transfer/       # Schema transfer role
│   ├── oracle_backup/         # Backup role
│   ├── oracle_tablespace/     # Tablespace management role
│   ├── backup_schedulePure/   # Backup scheduling role
│   ├── oracle_awr/            # AWR report role
├── README.md                  # Project overview
├── .gitignore                 # Ignored files
└── LICENSE                    # MIT License
```

## Prerequisites
- Oracle 19c non-CDB database
- Ansible 2.9+
- VirtualBox 7.0, Oracle Linux 8
- Oracle 19c binaries (LINUX.X64_193000_db_home.zip), patch files
- Host: 16GB RAM, 100GB disk, 4+ core CPU

## Author
**Thomas Senesie**  
Oracle DBA with expertise in Ansible automation.  
- Email: senesiethomas02@gmail.com  
- GitHub: [ThomaSenesie](https://github.com/ThomaSenesie)

## License
MIT License - see [LICENSE](LICENSE).
