A stream is composed of mutliple shards/partitions. Up to 365 days of data retention, but by default is usually 24 hours. Data can be reprocessed/replayed. Once data is inserted in Kinesis, it can be deleted, and the records can be up to 1Mb.

## Capacity Modes
**Provisioned mode**: Manually choose the number of shards provisioned, scale manually or using API. Each shard gets 1Mb/s input or 1000 records/s, and it gets 2Mb out. Why the difference? ^1b195a

You pay per shard provisioned per hour.

**On demand**
Automatically adjusted on demand, automatic scalling based on the throughput peak during the last 30 days. Pay per stream per hour and data in/out per Gb. Default capacity provisioned is 4Mb/s or 4000 records per second.

Limits:

Producers can send 1Mb/s per second to each shard.
Consumers can read up to 2Mb at read per shard across all consumers. 5 API calls per second per shard across all consumers. This means that the only way to scale is to add shards.

### Video Streams
Producers: Security camera, body-worn camera, AWS DeepLens, smartphone camera, audio feeds, images, RADAR data, RTSP cameras, etc.

One producer per video stream. Video can be replayed.
Consumers: your own, [[SageMaker]], or [[Rekognition]] Video.

It keeps the data from 1 hour to 10 years.

Example:
![[Pasted image 20231007153839.png]]
