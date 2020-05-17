# AWS Developer Asscociate Exam Preparation - DVA-C01

### Udemy Course

* Link - https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/ 

### Notes
* IAM global scoped
* pem file default permission - 0644 change it to 400
* ami specific region

### ELB
* ELB - Client ip can be seen by X-Forward-For from load balancer request

### EBS & EFS
* Elastic Block Stoarge are  availability zone scoped
* Boot volume only GP2 SSD and IO SSD
* GP2 - IO increases if disk size increses
* IO - can increase IO independently 
* EBS - can be attached only to one EC2
* To use EBS in another AZ take a snapshot and move to another AZ and use it
* Instance Store - Very high IOps,low latency - Not reliable - Physical device attached to EC2
* GP 2 - 3 IOPS/GiB, with the ability to burst to 3,000 IOPS 
* IOPS SSD volumes support up to 64,000 IOPS 
* EFS - Multi az - 3 * prize of EBS - only support linux
* Multiple EC2 instances can share the same EFS
* EFS - supports life cycle management - Standard - Frequent access data, Infrequent Access data - > Move data to EFS -IA after N date is supported
* Configure SG before use it [ Allow SG of EC2 instance, so that EC2 can share the EFS]

### RDS 
* Can't ssh EC2, manual db patching, os patching [ AWS responsibilty]
* Automated backups, Read replicas to improve performance [read scalabilty], bw same az, region, multi region
* Multiple az to improve reliability and Disaster recovery
* EBS - GP2, IO EBS
* Async - replication to sysnch data bw replicas can't write data
* MUlti AZ - Disaster Recovery - Not for scalabilty [ This is not read replicas]
* Read replicas can be setup in multi AZ
* DB name must be unique across region
* If master DB not encrypted, then we can't encrypt read replicas
* At rest encryption by keys - For the first time while create
* If you want to encrypt db after creation, create snapshot -> copy snapshot to encrypt snapshot -> create db from encrypt snapshot -> migrate to new db -> delete old un-encrypt db
* Flight encryption by SSL
* Force ssl in postgres [ rds.force_ssl=1] and in mysql GRANT USAGE ON *.* 'mysqluser'@'%' REQUIRE SSL
* snapshot of (non)encrypted DB is (non)encrypted
* Can copy snapshot to encrypted snapshot
* IAM based login Mysql and postgres
* Transparent data encrytioon supports only for oracle, MSSQL

#### Aurora DB
* Aurora -support both Mysql, postgres driver
* 5* mysql, 3* Postgres - 15 read replicas [ only 5 in mysql and postgres]
* 20 % cost more that RDS. Store data in shared volume. Auto expand from 10GB to 64TB
* 6 copies of data in 3 az [ 4 for writes, 3 for read, data stored on 100 volumes]
* supports cross region replcation
* Restore data without backups 
* It provides 2 endpoints. 1 for write and one for read [ use correctly]
* Auto scale based on cpu usage [ read replicas upto 15 ]
* Aurora serverless - No capacity planning needed, suitable for infrequent, unpredictable workload, pay per second, cost effective, auto DB instaciation, client connect via proxy fleet
* Global Aurora - Multi region, 1 primary region[read/write], upto 5 secondary[read only]

### ElasticCache
* Managed Redis, memcached
* User session storage and store cache
* Stores data in memory. So it is very fast, low latency
* Multi AZ - Auto failover [ Redis not Memcached]
* Redis is like RDS but Memchached -Multinode partioning sharding, Multi threaded architecture, no backup & restore, 
* Lazy loading/ cache aside - If no cache query DB and store in cache. Data outdated, IF no cache latency, -  data that is often requested will be loaded in ElastiCache
* Write through - If any DB write, write in cache also. So no latency. this has longer writes, but the reads are quick and the data is always updated in the cache
* Cache Eviction and Time to Live [TTL] - set time for cache, kill it after that - gaming, leader board