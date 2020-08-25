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

### Multi-AZ vs Read-Replica

- Multi-AZ is not used to support the reads
- The failover happens only in the following conditions:
    - Primary DB fails
    - An AZ outage
    - the DB instance server type is changed
    - the operating system of the DB instance is undergoing software patching
    - a manual failover of the DB instances was initiated using reboot with failover
    
- No failover for DB ops (long runnin queries, deadlocks, or database corrups errors)
- Endpoint is the same after failover (no URL change in app needed)

- Lower maintenance impact it happens on the standby, which is then romoted to master
- Backups are created from then standby (doesnt impact master perf)
- Multi AZ is only within a single region, not cross region. Region outages impact availability.

- RDS Read Replicas 
- Read replicas help scaling read traffic
- A Read Replica can be promoted as a standalone database (manually)
- Read Replicas can be within AZ, Cross AZ or Cross Region
- Each Read Replica has its own DNS endpoint
- You can have Read Replicas of Read Replicas
- Read Replicas can be multi-az
- Read Replicas help with DR by using a cross-region RR
- RDS Read Replicas are not supported for Oracle
- Read Replicas can be used to run BI / Analytics Reports for example


### DB Parameter Groups

- You can configure the DB engine using Parameter Groups
- Dynamic parameters are applied immediately
- Static parameters are applied after instance reboot
- You can modify parameter group associated with a DB (must reboot)
- See docs for list of params for a DB tech

- Must know parameter: 
    - postgreSQL|SQL: `rds.force_ssl=1` -> force SSL connections
    - MySQL|MariaDB: `GRANT SELECT ON mydatabase.* TO 'myuser'@'%' IDENTIFIED BY'....' REQUIRE SSL;

### RDS Backup vs Snapshots
- Backups
    - Continuous and allow Point in Time recovery
    - Backups happend during maintenance windows
    - When you delete a DB instance you can retain automated backups
    - Backups have a retention period you set between 0-35 days
- Snapshots
    - Takes a lot of IO operations and can stop the db from seconds to minutes
    - Snapshots take on MultiAZ db dont impact the master - just the standby
    - Snapshots are incremental after the first snapshot (whichi is full)
    - You can copy & share DB Snapshots
    - Manual Snapshots don't expire
    - Tyou can take a 'final snapshot' when you delete your DB

### RDS Security

- Encryption at rest:
    - Can only be enabled when you first create the DB instnace
    - If you want to encrypt a non-encrypted db
        - snapshot
        - copy snapshot as encrypted
        - create DB from snapshot
- Your Responsiblity
    - check the ports/ip/SGs inbound rules in DB's SG
    - In-database user creation and permissions
    - Creating a db with or w/o public access
    - Ensure parameter groups or DB is configured to only allow SSL connections
- AWS Resp
    - No SSH access
    - No manual DB patching
    - No manual OS patching
    - No way to audit the underlying instance

### RDS API for SysOps

- `DescribeDBInstances` API
    - Helps to get a list of all the DB instances you have deployed including Read Replicas
    - Helps to get the DB version
- `CreateDBSnapshot` API
- `DescribeEvents` API
- `RebootDBInstance` API - Helps to initiate a "forced" failover by rebooting the instance

### RDS with CloudWatch

- Cloudwatch metrics associated with RDS 
    - DatabaseConnections
    - SwapUsage
    - ReadIOPS/ WriteIOPS
    - ReadLatency / WriteLatency
    - ReadThroughPut / WriteThroughPut
    - DiskQueueDepth
    - FreeStorageSpace
    
- Enhanced Monitoring (gathered from an agent on the DB instance)
    - Useful when you need to see how diff processes or threads use the CPu
    - Access to over 50 new CPU, memory, file system, and disk I/O metrics

### RDS Performance Insights
- Visualize your db performance and analyze any issues that affect it
- With the perf isnight dashboard, you can visualize the db laod and filter the load:
    - By Waits -> find the resource that is the bottleneck (CPU, IO, lock...)
    - By SQL statements -> find the SQL statement that is the problem
    - By Host -> find the server that is using the most our DB  
    - By Users -> find the user that is using the most our DB
- DB Load = number of active sessions for the DB engine
- You can view the SQL queries that are putting load on your database

## Amazon Aurora

- Proprietary (no open source)
- Postgres n MySQL are both supported as aurora db (you can connect to aurora as postgresql or mysql)
- Aurora is AWS cloud Optimized (5x per over mysql, 3x perf over postgres)
- Aurora Storage automatically grows in increments of 10GB up to 64TB
- Aurora can have 15 replicas while MySQL has 5, and the replication process is faster
- Failover in aurora is instantenous
- Aurora costs more than RDS (20% more) but worth it

### Aurora HA and Read Scaling
- 6 copies of your data across 3 AZ
    - 4 copies out of 6 needed for writes
    - 3 copies out of 6 need for reads
    - Self healing process with peer-to-peer replication
    - Storage is striped across 100s of volumes
- One aurora instnace takes write (master)
- Automated failover for master in less than 30 seconds
- master + up to 15 aurora read replicas serve reads
- Support CRR 

- "Writer endpoint" always point to the master
- AutoScaling supported for Read Replicas
- "Reader endpoint" load balanced to read replicas

- automatic failover
- backup n recov
- isolation n sec
- industry compl
- push-button scaling
- automated patching w zero downtime
- advanced monitoring
- routine maintenance
- backtrack: restore data at any point of time without using backups

#### Security
- Encrpytion at rest using KMS
- Autoamted backups, snapshots and replicas are also encrypted
- Encrpytion in flight SSL
- Authentication using IAM
- You are responsible for protecting the instance w SGs
- cant SSH

#### Aurora serverless
- no need to choose an instance size
- Only supports MySQL 5.6 & Postgres
- Helpful when you cant predict the workload
- DB clusters starts, shutdown and scales automatically based on CPU/conns
- Cna migrate from Aurora cluster to Aurora serverless and viceversa
- Aurora serverless usage is measured in ACU (aurora capacity units)
- billed in 5 mins increments of ACU
- Some features of aurora arent support on serverless mode (check docs)

## AWS ElastiCache 
- same way RDS is to get managed relation db
- ElastiCache is to get managed Redis or Memcached
- Caches are in-memory dbs with really high performance, low latency
- Helps reduce load off of dbs for read intensive workloads
- helps make your app stateless
- Write scaling using sharding
- read scaling usin read replicas
- multi az w failover capability
- AWS takes care of OS maintenance / patching, opt, setup, config, monitoring, failure recov n backups


#### Use cases for ElastiCache
- apps queries elasticache if not avail get from rds and store in elasticache
- helps relieve load in rds
- cache must have invalidation strategy to make sure only the most up to date data is used in there

- users log into any of the app
- the app writes the session data into elasticache
- the user hits another instance of our app
- app gets data from elasticache

### ElastiCache Redis vs Memcached
- Redis
    - MultiAZ with auto-failover
    - Read Replicas to scale reads and have high availability
    - Data durability using AOF persistence
    - Backup and restore features
    - REPLICATION

- Memcached
    - Multi-node for partitioning of data (sharding)
    - Non persistent
    - No backup and restore
    - Multi-threaded architecture
    - SHARDING

