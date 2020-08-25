## AWS RDS Overview

### RDS

- RDS stands for relational db
- Managed db service for db use SQL as a query language
- Allows:
    - Postgres
    - Oracle
    - MySQL
    - MariaDB
    - Microsoft SQL
    - Aurora

### Advantage over using RDS vs DB on EC2
- Managed service:
    - OS patching level
    - Continuous backups and restore to specific timestamp (PIT restore)
    - Monitoring dashboards
    - Read Replicas for improved read performance
    - Multi AZ setup for DR
    - Maintenance windows for upgrades
    - Scaling capability
    - But you cant SSH into your instances

### RDS Read Replicas for read scalability
- Up to 5 Read replicas
- Within AZ, Cross AZ or Cross Region
- Replications are ASYNC so reads are eventually consistent
- Replicas can be promoted to their own DB
- Applications must update the connection string to leverage read replicas

### RDS Multi AZ for Disaster Recovery
- Sync Replication (to the standby database)
- One DNS name - automatic app failover to standby
- Increase availability
- Failover in case of loss of AZ, loss of network, instance or storage failure
- No manual intervention in apps

### RDS Backups
- Backups are automatically enabled in RDS
- Automated backups
    - Daily full snapshot
    - Capture transaction logs in real time
    - -> ability to restore to any point in time
    - 7 days retention
- DB Snapshots:
    - Manually triggered by the user
    - Retention of backup for as long as you want

### RDS Encryption
- Encryption at rest capability with AWS KMS - AES-256 Encryption
- SSL certificates to encrypt data in flight 
- To enforce SSL
    - PostgreSQL `rds.force_ssl=1` - in the AWS RDS CONSOLE parameter groups
    - MySQL within the DB:
        - `GRANT USAGE ON *.* to 'mysqluser'@'%' REQUIRE SSL;`
- To connect using SSL
    - Provide the SSL Trust certificate (can be downloaded from AWS)
    - Provide SSL options when connecting to the database

### RDS Security
- RDS databases are usually deployed within a private subnet not in a public
- RDS security works by leveraging sec groups ( the same concept for ec2 instances) - it controls who can communicate with RDS
- IAM policies help control who can manage AWS RDS
- Traditional username/pw can be used to login to the database
- IAM users can now be used too (for mysql/aurora)

### RDS vs AURORA
- Aurora is propietary tech from AWS
- Postgres and MySQL are both supported as Aurora DB (means drivers will work as if aurora was a postgres or mysql db)
- Aurora is cloud optimized - claims 5x performance improvement over mysql and over 3x performance over psotgres
- Aurora storage automatically grows in increments of 10gb up to 64tb
- Aurora can have 15 replicas while MySQL has 5, and the replication process is faster (sub 10ms replica lag)
- Failover in Aurora is instantaenous. It's HA native.