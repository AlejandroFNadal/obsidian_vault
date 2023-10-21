- Store data on virtual drives [[EBS]]
- Distribute loads across machines [[ELB]]
- Scale services with a [[ASG]]

It can run Linux, Windows and even MacOS.

Things to choose:
- RAM
- CPU power
- Storage spaces: Network attached ([[EBS]] or [[EFS]]), or physical hardware with the EC2 Instance Store
- Network card: speed, and whether or not we have a public [[IP]]
After creation, each instance receives an instance id, a public [[IP]] address and a private IP. 

The default user is ec2_user

You get charged by second increments, the minimum time is 60 seconds (as soon as the instance starts you get charged that.)
## EC2 security groups

Control how traffic is allowed inside EC2 instances. They only have allow rules, they can have IP or security groups.

It acts as a firewall, controlling port access, authorized IP ranges and I/O traffic.
They can be attached to multiple isntances. They can be locked down to a [[Region]] or to a [[VPC]]

When traffic gets blocked, the EC2 instance won't even see it. Refused traffic eventually gets a timeout. 

A security group can be configured to accept traffic from another security group. 

They have a name and an ID.

### EC2 User Data

Script that runs when the machine starts. It has to be base64 encoded, if we set in [[Cloudformation]] or in [[SAM]] , we have to use Fn::Base64 function. 
The script runs as root and it can be used to download files, install updates and software.


The script can be configured to run both on every startup or only in the first startup.

It cannot be modified if the instance is running.

## Naming convention

![[ec2_naming_convention.png]]

### Types of instances

- General purpose: C class, compute optimized:
	- Batch processing workloads
	- Media transcoding
	- High performance webservers
	- HPC
	- Machine learning, scientific modeling
	- Dedicated gaming services
- Memory Optimized, R and X series:
	- Relational / Non relational in memory databases
	- Distributed web scale cache stores
	- Real time processing of big unstructured data
- Storage optimized: I Class
	- High frequency online transaction processing: OLTP
	- [[DFS]]
	- Relational and NoSQL
	- Cache DB: Redis -- Warehousing
### Purchasing Options

- **On demand instances**: short workload, predictable pricing, pay by second
- Reserved (1-3 years) long workloads: Convertible reserved instances: long workloads with flexible instances. Up to 72% discount compared to on demand. Payment is upfront, partially upfront or no upfront, with different levels of savings each. You can request a regional or zonal instance scope??. You can try to sell or buy reserved instances at a cheaper price.
- Saving plans: you commit to an specific amount of usage. For example, $10/hour for one year. Any usage beyond commitment is billed for On Demand Price. It is locked to the instance family in a particular region. For instance, you might be locked to M5 in eu-east-1, but you can change Tenancy, OS, instance size, etc.
- Spot instances: short workloads, cheap, can lose instances(less reliable). Up to 90% savings. A spot price is defined and if the instance costs more than that, you lose it. Do Not put critical jobs or DBs there, only for workloads that are resilient to failure but longer than what you would put in a [[Lambda]].
- **Dedicated hosts**: book an entire physical server, controls instance placement. You can also get this in On-Demand or Reserved. Only makes sense with strong regulatory compliance needs or complicated licensing models.
- **Dedicated instances**: No other customer will share your hardware. Your VM might be rebalanced among machines, moved, etc. Might be enough to reach some requirements, otherwise go to dedicated hosts.
- **Capacity reservation**: on an specific [[AZ]] for a specific duration. No time commitment, no discount. It should be combined with Regional Reserved Instances and Saving Plans to benefit from discounts. Otherwise you are charged at on-demand rate. It is suitable for short term uninterrupted workloads that need to run in a specific [[AZ]]

## EC2 Instance Store

Drives connected directly to the server. Better I/O performance. They lose the content when the instance is stopped. Excellent for buffer, cache, scratch data, temporary content. There is a risk of losing the data if there is hardware failure, and the user is responsible for replicating the data. 

To compare, using [[EBS]] you can get 32000 IOPS, but using i3 series of the EC2 instance store, you can get 3.3 millon IOPS.