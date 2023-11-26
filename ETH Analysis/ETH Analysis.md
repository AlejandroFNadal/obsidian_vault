#eth
I am starting to suspect that the issue here is that the consumer needs to communicate with the broker, and it goes into a sort of invalid state between executions of the Lambda. Most likely, we should use the Kafka integration of AWS to read the data.

We also have a few blocks that have no transactions. Is this possible?: Yes, it is.

Kafka is using around 500Mb of ram and Airflow is using around a Gb.
We might need to paralelize the consumption by creating mutliple partitions.

Locally, the Kafka consumer can handle the producer running in Lambda.
When the consumer is created, only sets itself on a -1000 offset. That is not so great, it is skipping lots of stuff.