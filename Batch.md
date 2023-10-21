Run batch jobs as docker images.
The images are provisioned dynamically, EC2 and Spot instances.
Fully serverless. Just pay for the underlying EC2 instances.
They can be scheduled with CloudWatch events or orchestrated with AWS Step Functions

Difference between Batch and [[Glue]]:

- Mostly Apache Spark focused.
- In batch, any computing job is valid, that can be put in Docker.
