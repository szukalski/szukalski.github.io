{
  "title": "AWS Certified Database Specialty",
  "summary": "",
  "date": "2022-01-26T12:09:51Z",
  "lastmod": "2022-01-26T12:09:51Z",
  "tags": [
    "notes",
    "aws"
  ],
  "categories": [
    "aws",
    "study"
  ],
  "draft": true
}

## AWS Databases

### Database Types

* Relational
  * Structured data
  * Atomic operations
  * Bad for semi-structured data
* Key-Value
  * Extremely scalable
  * High throughput, low latency
  * Flexible schema
  * Should know access pattern
  * Not good for analytics
* Document
  * JSON-like documents
  * Flexible schema
  * Aggregate across documents
  * Bad for highly relational data
* In-Memory
  * Microsecond latency
  * No persistence
* Graph
  * Easily relate sets of data
  * Flexible schema
  * Bad for high volume of transactions
* Time Series
  * Monitoring
  * Time-ordered data
* Ledger
  * Immutable and transparent
  * Highly scalable
  * Bad for decentralized architectures

* OLTP - Transactions
  * Online transaction processing
  * Fast queries, high volume
* OLAP - Analytics
  * Online analytical processing
  * Complex queries that aggregate data
  * Slower queries

### RPO and RTO

* RPO
  * Amount of data we can lose
* RTO
  * How long we can survive without data

## RDS

### Multi-AZ

* Primary + Standby
  * Standby has no running engine until failover
* Failover downtime
  * Single-AZ : 3-5 mins
  * Multi-AZ : ~1 min

### Backups

* Automated backups
  * Enabled by default
  * 30 minute "backup window"
  * 1-35 days retention
    * Default 7-days
  * Single-AZ may have I/O latency during backup
  * Don't include information about parameter or option groups
  * To share, copy snapshot to new snapshot
  * Backups deleted on database delete by default
* System-wide backup
  * Not individual database
* Manual snapshots stored in S3
  * Stored until deleted
  * Snapshot on single-AZ may give I/O latency
  * Incremental backup
  * Restore time depends on amount of data
  * Can be shared as-is
* Point-in-Time Restore
  * Requires automated backups
  * PITR restores to new database instance and then replays transaction logs before cutting over to new instance
* Multi-AZ Impact
  * Performed on standby, so no latency
  * SQL Server experiences latency no matter what
* Snapshot copies
  * Considered manual snapshots
  * Destination account / region needs access to KMS key
    * Need to use CMK
  * Need to make copy of encrypted snapshot before you can launch a database instance

### Monitoring

* Events and Event Notifications
  * Event notification > SNS > Lambda
* Enhanced Monitoring
  * CloudWatch gets metrics from hypervisor, Enhanced Monitoring gets metrics from instance
    * Free memory, process details
* Performance Insights
  * Counter metrics
  * Database load
    * Compares database load to instance capacity
  * Top items
* Instance Statuses
  * Ie. failed, restore-error, incompatible, maintenance

### Logs

* MySQL / MariaDB
  * Error
  * General_log
    * Enabled in parameters
  * Slow_log
    * Logs queries over X threshold set in parameters
* PostgreSQL
  * PostgreSQL
* Oracle
  * Audit
  * Alert
  * Trace
  * Listener
* SQL Server
  * Error
  * Trace
  * Agent
  * Dump

#### MySQL and MariaDB

* Binary Log (binlog=
  * Configurable retention with ```rds_set_configuration```
* Configurable ```log_output```
  * FILE
    * ```general_log``` and ```slow_query_log``` written to file system
    * Rotated hourly
    * Needed to view from console
  * TABLE
    * ```general_log``` written to ```general_log``` table
    * ```slow_query_log``` written to ```slow_log``` table
  * Ability to rotate logs

#### PostgreSQL

* ```log_statement```defines which SQL statements to log
* ```log_min_duration``` defines threshold for when query is logged
* ```log_retention_period``` in minutes up to 7 days

#### Oracle

* Supplemental logging
  * ```rdsadmin_util.alter_supplemental_logging```
  * Switch log files with ```rdsadmin_util.switch_logfile```
* Force logging
  * Log all changes except those in temporary namespaces
  * Enabled by stored procedure

#### SQL Server

* Error and Agent logs
  * Console, CLI, or stored procedure ```rds_read_error_log```
  * Retained for 7 days

### Security

* External authentication
  * Kerberos or AD
  * MySQL, SQL Server, Oracle, PostgreSQL
* IAM authentication
  * MySQL, PostgreSQL
  * Enabled during creation or with ```modify-db-instance```
  * MySQL limited to 200 connections/second
  * PostgreSQL requires SSL parameter set to 1
  * User needs to be created in DB and granted rights for IAM access
  * Uses generated authentication token
    * ```aws rds generate-db-auth-token```
    * Token life is 15 mins
* KMS
  * Can only enable encryption at creation
  * Read replica encryption must be same as primary
  * Copied snapshots of encrypted DB is encrypted
  * KMS keys are region specific
* SSL/TLS
  * RDS CA-2019 enabled for new instances
  * Can force SSL with ```rds.force_ssl 1```
  * ```SSL OFF``` error usually means client is trying to connect without SSL

### Resource Modification

* Storage Change
  * 6 hr wait between storage modifications
  * Increase must be minimum 10%
  * Can't reduce the storage
    * Have to export and recreate
  * Outage inducing
    * Any change to or from Magnetic
    * Single AZ IO1 <-> GP2
* Instance Change
  * Single AZ : Short outage
  * Multi-AZ : AZ failover
* Parameter Groups
  * Engine configuration values
  * Static and dynamic
    * Static needs reboot
  * Cannot modify default parameter, have to create customer parameter group and associate it with the instance
* Option Groups
  * Enable additional features
  * Persistent and permanent options
    * Permanent cannot be changed afterwards
  * Links to VPC instead of instance

### Maintenance

* Engine upgrade needs outage
* Minor version backward-compatible
* Major version not backward-compatible

### Troubleshooting RDS

* Parameter group needs reboot to take effect on static parameters
* MySQL replication
  * Replication only supports InnoDB storage engine
  * Ensure replica is read only
  * If lag is significant, create new read replica
  * Ensure source retains binary logs long enough for replicas to process changes
* Postgres replication
  * Ensure `wal_keep_segments` is large enough to retain WAL files (Write-Ahead Log) for all replicas
  * Consider increasing `max_wal_senders` for multiple replicas
  * TransactionLogsDiskUsage, Oldest Replication Slot Lag
* Too many connections
  * Increase `max_connections`
  * Modify instance to larger size
  * Implement connection pooling on application

### Pricing

* Instance
* Storage
* I/O (Magnetic)
* Backup Storage
* Data Transfer
* Licensing

## DynamoDB

### Architecture

* Components
  * Tables are a collection of items
  * Items are a unique group of attributes
  * Attributes is a functional data element
* Primary Key
  * Partition Key (aka Hash Attribute)
    * Determines which partition
  * Partition Key + Sort Key
* Secondary Index
  * Global Secondary Index
    * Partition key and sort key can be different than table
  * Local Secondary Index
    * Must be created during table creation
    * Same partition key as table, but different sort key
* Data stored in partitions
  * Allocation of storage for table
  * Hash function determines partition using partition key as input
    * Should be as unique as possible
  * One partition holds 10GB and supports up to 3,000 RCU or 1,000 WCU
* Items
  * Limited to 400KB
  * Data types
    * Scalar
      * Single value
    * Document
      * List
        * Ordered collection of values
      * Map
        * Unordered collection of name-value pairs (like JSON)
      * Set
        * Multiple scalar values of same type
* Tables
  * Unique name per account and region
  * Case sensitive

### Performance

* Capacity
  * On-Demand
  * Provisioned
    * Consistent and predictable performance
    * RCUs & WCUs
  * Same limit (40K RCU/WCU)
  * Switch once every 24 hrs
* RCU
  * 1 strongly consistent read / second = 1 RCU per 4KB item
  * 2 eventually consistent read / second = 1 RCU per 4KB item
  * 1 transactional read = 2 RCU per 4 KB item
* WCU
  * 1 write / second = 1 WCU per 1KB
  * 1 transactional write / second = 2 WCU per 1KB

### Scans and Queries

* Foundations
  * Filtering does not reduce RCU usage, simply discards data
  * Eventually consistent by default, can use ```ConsistentRead```
  * Single query returns results within 1MB, can use pagination
  * Can limit results to reduce RCU
  * Prefer query over scan
  * Consider a secondary index if you repeatedly use scans to filter on the same non-PK/SK
* Scan
  * Return all items and attributes for given table
  * Parallel scans can be used to improve performance
* Query
  * Find items based on PK values
    * Optional SK and comparison operator to refine results
  * Limited to PK, PK+SK, or secondary indexes

## Resources

[AWS Certified Specialty](https://aws.amazon.com/certification/certified-database-specialty/)
[Exam Guide](https://d1.awsstatic.com/training-and-certification/docs-database-specialty/AWS-Certified-Database-Specialty_Exam-Guide.pdf)
[Sample Questions](https://d1.awsstatic.com/training-and-certification/docs-database-specialty/AWS-Certified-Database-Specialty_Sample-Questions.pdf)
[10 study tips for the AWS Certified Database – Specialty Certification](https://aws.amazon.com/blogs/training-and-certification/10-study-tips-for-the-aws-certified-database-specialty-certification/)
[AWS Database Products](https://aws.amazon.com/products/databases/)