awslogs is a great tool to get logs, otherwise the logs from aws are kinda useless. To use it:

```
awslogs get /aws/lambda/eth-analysis-ProducerFunction-wkV8d81VZ7aq -s 2023-11-12 00:00:00 -e 2023-11-20 00:00:00> producerlogs.log
```
The parameter after the get is the log group.