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

### S3
* Buckets - Globally unique name, Stores as objects, Region scoped
* Naming - no underscore, no uppercase, 3-63 characters
* s3://my-bucket/akilan.txt
* max object size - 5TB, use multi-part upload for more than 5GB data
* version enable in bucket level
* 4 encryption methods - SSE- S3, SSE-KMS, SSE-C, client side encryption
* SSE- S3 - AWS managed, encryption at server side, AES 256, set header x-amz-server-side-encryption="AES256"
* SSE - KMS -  user control, audit trail, encryption at server side, set header x-amz-server-side-encryption="aws:kms"
* SSE - c- encryption key stored in client location not in aws, send encryption key in header, So client need to send data + key. While retrieve send key as well. https is mandatory
* client side - data encrypted in client side and send the sncrypted data to s3. 
* S3 - Websites - $BUCKET_NAME.s3-website.$REGION.amazonaws.com
* enable CORS for cross origin
* S3 is eventually consistent - PUT 200 -> GET 200, GET 404 -> PUT 200 -> GET 404, PUT 200 -> PUT 200 -> GET 200 (old version), DELETE 200 -> GET 200 [ Takes time to delete]
* No way for strong consistency

### Route 53
* Managed DNS service
* A hostname to IPV4, AAAA - hostname to IPV6, CNAME hostname to hostname, Alias - Hostname to AWS resource
* TTL - time to live is mandatory for route 53
* cname - works only on non root domain [ subdomain ]
* Alias - Works for both root and non root domain. Free, native health check
* Split traffic is possible in route 53. Percentage wise we can split
* Simple Routing policy - Random trafic
* Weighted Routing Policy - Percentage wise we can split
* Latency Routing policy - Based on low latency 
* Health checker
* Failover routing policy [ Primary and Secondary ]
* GeoLocation Routing policy - Different from Latency policy [Specify traffic from specifi country]
* Multi value policy - Kind of load balancer [ALB] with health check

### VPC
* VPC - Region scoped
* Subnets - Availabilty scoped
* Internet Gateway - Responsible for connecting EC2 instances from public subnet to internet
* NAT Gateway - Responsible for connecting EC2 instances from private subnet to internet
* Network ACL - Firewall controls trafic from and to subnets. Both allow and deny. Rules only IP address based [subnet level]
* Security Group -  Firewall controls trafic from and to SNI to EC2 . Allows only. Rules based on IP and Another SG [EC2 instance level]
* VPC flow logs - Capture all the networking activities as logs. Helps to troubleshoot n/w issues, Can go to S3, Cloudwatch logs
* VPC peering - Connect 2 vpc privately using AWS network
* VPC Endpoints - Connect to AWS services using private AWS network not via public. Used within VPC
* Site-Site-VPN - Connect onpremise machine to AWS services. Goes via public network
* Direct Connect - Physical connection bw aws and on premise.Secure, Private network, Low latency
* Site-Site-VPN and Direct connect - Can't use vpc endpoints

### AWS cli
* Custom role by adding policy using aws policy creator UI
* Test role access by using policy simulator
* use --dry-run option in aws cli
* STS command line to decode error message
* aws sts decode-authorization-message --encoded-message 
* EC2 instance metadata url - http://169.254.169.254/latest/meta-data
* Role name only can be retrieved not IAM policy
* Meta data - infomation about EC2
* user data - Startup script
* aws configure --profile aws-office-aoount
* aws s3 ls --profile aws-office-aoount
* aws cli with MFA - Activate MFA for the user
* aws sts get-session-token --serial-number $arn-of-the-mfa-device --token-code $code-from-token - Get the response
* export AWS_ACCESS_KEY_ID=example-access-key-as-in-previous-output
* export AWS_SECRET_ACCESS_KEY=example-secret-access-key-as-in-previous-output
* export AWS_SESSION_TOKEN=example-session-token-as-in-previous-output
* us-east-1 will be the default region if we didn't specify
* Rate Limit - EC2 100 requests per second
* S3 get - 5500 GET per second per object
* For intermittent error [Throttling exception] -increase exponential backoff
* For consistent error - Increase rate limit
* service quota- 1152 vcpu, Can be increased by raising aws ticket
* AWS cli - 1st pref command line, 2nd pref ENV var, 3rd .aws/config and .aws/credentials -> 4th container credentials -> 5th EC2 instance profile credentials
* sigV4 - pass Authorization in request header or pass it as a query string to access s3