Network file system, can be mounted by multiple [[EC2]] instances in multiple [[AZ]]
Highly available, expensive, scalable (3 times gp2), you pay per use.

![[efs.png]]

Use cases: content management, web serving, data sharing, wordpress.
Uses NFS v4.1 protocol.
Uses a [[EC2#EC2 security groups]] to control access. It is only compatible with Linux, you can encrypt it at rest using [[KMS]].
Uses [[POSIX]], and that is what makes it imposible to use in non linux.
It grows at need and you pay per use.

## EFS Scaling
1000 concurrent NFS client, 10Gb/s throughput, grows to the petabyte level.

### Performance mode

This is the default, it is for latency-sensitive use cases like a webserver, CMS, etc. Higher latency, throughput, highly paralel.

### Throughput mode:
In this mode, the throughput is not limited by the storage size. 50MiB/s + burst of up to 100MiB/s, at 1 TiB storage.

## Storage Tiers
- Standard
- Infrequent: higher cost to retrieve, lower price to store. Called EFS-IA. Has to be enabled with a Lifecycle policy.

## Availability

- One zone, great for dev, the backup is enabled by default. It is compatible with EFS-IA and in that way you can get up to 90% in cost savings.

- Standard: multi [[AZ]], great for prod.