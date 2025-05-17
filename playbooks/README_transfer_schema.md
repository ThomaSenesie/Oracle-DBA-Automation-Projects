# transfer_schema.yml

## Purpose
Automates the transfer of the APP_USER schema from a production (PROD) to a test (TEST) Oracle 19c non-CDB database using Data Pump, ensuring secure and efficient schema migration. This playbook streamlines schema transfer for enterprise Oracle DBA automation.

## Features
- **Schema Export**: Exports the APP_USER schema (with CUSTOMERS, ORDERS, PRODUCTS objects) from PROD using expdp.
- **Secure File Transfer**: Copies the dump file from prod-server to test-server using scp with SSH key-based authentication.
- **User Creation**: Creates APP_USER_TEST on TEST with necessary privileges.
- **Schema Import**: Imports APP_USER into TEST as APP_USER_TEST using impdp with schema remapping.
- **Directory Setup**: Configures Data Pump directory (/u02/backup/dpump) on both servers.
- **Verification**: Confirms APP_USER_TEST and its objects on TEST.

## Role
- **oracle_transfer**
  - **Tasks**:
    - Cleans up existing dump files on both servers.
    - Creates and configures Data Pump directory (/u02/backup/dpump) with permissions.
    - Exports APP_USER schema from PROD to app_user_YYYYMMDD.dmp.
    - Copies dump file from prod-server to test-server using scp.
    - Imports schema into TEST, remapping APP_USER to APP_USER_TEST.
    - Verifies APP_USER_TEST exists on TEST.
  - **Variables** (in vars/main.yml):
    - oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
    - db_sid: PROD (for prod-server), TEST (for test-server)

## Usage
```bash
ansible-playbook playbooks/transfer_schema.yml -i inventory
```

## Verification
- **Check APP_USER_TEST on test-server**:
  ```bash
  ssh oracle@192.168.56.102
  export ORACLE_HOME=/u01/app/oracle/product/19.3.0/dbhome_1
  export PATH=$ORACLE_HOME/bin:$PATH
  export ORACLE_SID=TEST
  sqlplus / as sysdba
  SELECT username FROM dba_users WHERE username = 'APP_USER_TEST';
  SELECT object_name, object_type FROM dba_objects WHERE owner = 'APP_USER_TEST';
  SELECT count(*) FROM APP_USER_TEST.customers;
  SELECT count(*) FROM APP_USER_TEST.orders;
  SELECT count(*) FROM APP_USER_TEST.products;
  EXIT;
  ```
  - Expected: APP_USER_TEST exists, lists CUSTOMERS, ORDERS, PRODUCTS tables with non-zero row counts matching PROD.
- **Check Import Log**:
  ```bash
  cat /u02/backup/dpump/app_user_imp.log
  ```
  - Expected: Successful import with no errors.

## Notes
- Requires SSH key-based authentication from root@prod-server to oracle@test-server.
- Data Pump directory (/u02/backup/dpump) must exist with oracle:oinstall ownership.
- Supports PROD to TEST schema transfer.
