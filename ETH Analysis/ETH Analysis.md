#eth
I am starting to suspect that the issue here is that the consumer needs to communicate with the broker, and it goes into a sort of invalid state between executions of the Lambda. Most likely, we should use the Kafka integration of AWS to read the data.

We also have a few blocks that have no transactions. Is this possible?: Yes, it is.

Kafka is using around 500Mb of ram and Airflow is using around a Gb.
We might need to paralelize the consumption by creating mutliple partitions.

Locally, the Kafka consumer can handle the producer running in Lambda.
When the consumer is created, only sets itself on a -1000 offset. That is not so great, it is skipping lots of stuff.

## Tasks
- Add a swap file in the userData script. 
- Separate Kafka and Airflow to run in two different VMs, run the consumer function in the same place as the Kafka and test over time if we get oom-killer thingies.
- Create a lambda function that checks the data to see if we are missing any blocks in S3 in the past and if we are, trigger

## Deal with missing stuff
First, detect it. It can be missing in the S3: for this, we can create a lambda that goes over the content of the S3 and checks for missing stuff. However, it cannot check the whole thing over and over, so what is necessary is to store somewhere the already checked section. The table with BlockConfig info could be used for that.

Then, we could have gaps in Dynamo. For this one, perhaps there is some process that can be used in the machine that runs airflow, some autodetection and rerun of the old stuff. 

## Most urgent thing:
Finish the bloody front.
