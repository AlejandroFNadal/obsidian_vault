 Persistent data for [[EC2]]. It can be mounted only by one instance at a time and it is bounded to a [[AZ]]
 There is more latency than with an EC2 Instance store.

It is provisioned in Gb or in IOPS.
![[ebs.png]]

The content can be deleted on instance termination. This is the default behavior for the root EBS of a [[EC2]]. For non root disks, this is disabled by default.

From the [[EC2]] you need to create a filesystem init (for non root EBS):

```
mkfs -t ext4 /dev/new_volume
```

Then you mount it with the mount command.

## Snapshot
Not necessary to detach but recommended. To store it, there is a second alternative called EBS Snapshot Archive, which is 75% cheaper, but it requires from 24/72 hours for restoring. There is also a recyling bin to retain deleted snapshots, from 1 day to 1 year.

A EBS cannot be deleted if it is attached.

EBS can be encrypted, for free if it is a default key or by the cost of the key itself if we use custom keys. It is [[Symmetric encryption]]
## Moving content to another AZ.

Use a snapshot. 

## EBS Types

EBS are characterized by Size / throughput / IOPS.
- gp2-gp3 (SSD): balanced price and performance for a variety of workloads. Allowed for boot. They can go from 1GiB - 16 TiB. 
	- gp3: Baseline is 3000 IOPS
- io1/io2 (SSD): highest performance SSd for low latency - high throughput workloads. Allowed for boot.
- st1 (HDD): Low cost HDD volume designed for frequently accessed throughput intensive workloads.
- sc1 (HDD): Lowest cost for less frequently accessed workloads.
