# Aws-Dms-Mysql-to-Postgres-Migration.
"A project demonstrating a heterogeneous database migration from MySQL to PostgreSQL using AWS DMS and SCT."
---

### Step-by-Step Reproduction Guide

This guide outlines the major steps taken to complete the migration.

**Step 1: AWS Infrastructure Setup**

1.  **VPC:** A custom VPC (`DMSPVC-vpc`) was created with public and private subnets.
2.  **RDS Databases:**
    * An RDS for MySQL instance (`database-1`) was launched as the source.
    * An RDS for PostgreSQL instance (`database-2`) was launched as the target.
    * **Crucial Learning:** Both instances were configured to be in the **same VPC** and were made **publicly accessible** for the purposes of this tutorial.
3.  **EC2 Client Instance:** An Amazon Linux EC2 instance was launched into a public subnet of the same VPC to act as a client machine.
4.  **Security Groups:** Security group rules were meticulously configured to allow traffic on specific ports (22, 3306, 5432) from the necessary sources (My Home IP, the EC2 instance, the DMS instance).

**Step 2: EC2 Instance Configuration & Source DB Population**

1.  Connect to the EC2 instance via SSH.
    ```bash
    # Replace with your EC2 IP and key file path
    ssh -i "DDS.pem" ec2-user@YOUR_EC2_PUBLIC_IP
    ```
2.  Install required tools on the EC2 instance.
    ```bash
    sudo yum install git -y
    sudo dnf install mariadb105 -y
    sudo dnf install postgresql15 -y
    ```
3.  Download the sample database scripts.
    ```bash
    git clone [https://github.com/aws-samples/aws-database-migration-samples.git](https://github.com/aws-samples/aws-database-migration-samples.git)
    ```
4.  Navigate to the script directory and run the population script against the source MySQL database.
    ```bash
    # Navigate to the correct directory
    cd aws-database-migration-samples/mysql/sampledb/v1/

    # Run the script (replace endpoint and use the correct password)
    mysql -h YOUR_MYSQL_ENDPOINT -P 3306 -u admin -p < install-rds.sql
    ```

**Step 3: AWS SCT Configuration & Schema Conversion**

1.  **Local Setup:** Installed the AWS Schema Conversion Tool (SCT) and the required MySQL & PostgreSQL JDBC drivers on a local machine.
2.  **IAM User:** Created a dedicated IAM user (`sct-user`) with `AmazonRDSFullAccess` and `AWSSecretsManagerReadOnlyAccess` policies for programmatic access.
3.  **SCT Project:** Created a new migration project in SCT, connecting to the source and target RDS databases.
4.  **Conversion:**
    * Right-clicked the `dms_sample` schema in the source panel and chose **"Convert schema"**.
    * Right-clicked the converted schema in the target panel and chose **"Apply to database"** to create the empty table structure in PostgreSQL.

**Step 4: AWS DMS Task Creation & Execution**

1.  **DMS Components:** Configured a DMS Replication Instance and the Source/Target Endpoints.
2.  **Migration Task:** Created a new DMS task with the following key settings:
    * **Migration type:** `Migrate existing data` (Full Load).
    * **Target table preparation mode:** `Do nothing` (since SCT already created the tables).
    * **Table mappings:** Created a selection rule to include all tables (`%`) from the `dms_sample` schema.
3.  The task was started, and its progress was monitored via the "Table statistics" tab until the status showed **"Load complete"**.

**Step 5: Final Verification**

1.  Connected to the EC2 instance again and used the `psql` client to connect to the target PostgreSQL database.
    ```bash
    # Connect to the target database (replace endpoint)
    psql --host=YOUR_POSTGRESQL_ENDPOINT --port=5432 --username=postgres --password --dbname=postgres
    # AWS Database Migration: MySQL to PostgreSQL

---

## Phase 1: Infrastructure Provisioning

In this phase, the core AWS resources were created and configured.
* **VPC:** A custom VPC (`DMSPVC-vpc`) was created...
* **RDS Databases:** Launched a MySQL source and PostgreSQL target instance, ensuring both were in the same VPC...
* **EC2 Client:** Launched an EC2 instance into a public subnet...
* **Security Groups:** Configured inbound rules for SSH (port 22), MySQL (3306), and PostgreSQL (5432)...

---

## Phase 2: Source Database Population

This phase involved preparing the source database with sample data.
1.  Connected to the EC2 instance via SSH.
    ```bash
    ssh -i "key.pem" ec2-user@xx.xx.xx.xx
    ```
2.  Installed required tools (`git`, `mariadb`, `postgresql`).
3.  Cloned the AWS sample database repository.
    ```bash
    git clone ...
    ```
4.  Executed the `install-rds.sql` script to create and populate the `dms_sample` schema.

---

## Phase 3: Schema Conversion (SCT)

...(Ethe SCT steps: installing drivers, creating the project, converting the schema, and applying it to the target database)...

---

## Phase 4: Data Migration (DMS) & Verification

...( the DMS steps: creating endpoints, the replication instance, and the final migration task. Then, show the `psql` command and the final `SELECT` query that verified the data was moved successfully)...
    ```
2.  Ran a final query to confirm that the data was successfully migrated.
    ```sql
    -- This query should now return 1 row
    SELECT * FROM dms_sample.person WHERE last_name = 'User1';
    `<img width="2782" height="3840" alt="Untitled diagram _ Mermaid Chart-2025-09-01-183337" src="https://github.com/user-attachments/assets/927d8572-612c-4e79-9137-673c115c9386" />
``
    
![bf063530-5cff-4912-b26a-03cd295d39c4](https://github.com/user-attachments/assets/188eca25-2ccc-41b4-9537-e667e60fb369)
![bf063530-5cff-4912-b26a-03cd295d39c4](https://github.com/user-attachments/assets/dfc2924d-607f-4c7c-b
![4e83cd4d-ac09-4774-9745-aaa83e76d896](https://github.com/user-attachments/assets/b37441c9-72a6-4e0a-be1d-600edabd8499)
192-1e8c5d3347ca)[86979128-02f5-47b7-b989-24f044bc8a4c](https://github.com/user-attachments/assets/12a86ae4-b206-47fb-ab07-672bfb3!
[17d5a0f0-1e8e-465a-8bea-208609e6018d](https://github.com/user-attachments/assets/41b4c634-3b8f-4009-ba2e-48f07b62a4ec)
b6094)
