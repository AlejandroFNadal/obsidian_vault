---
tags:
  - AWS
---
Run code serverless.

**Free Tier** : 1 millon requests, 400000 Gb/s of compute time. 
After the free tier (renewed per Month), you pay 0.2 per another millon requests, then, 0.000002 dollars per request.  This may sound way too cheap: yep, it is. You also have to pay per duration:

After 400000Gb seconds fo compute time, you pay $1 per 600000Gb seconds. This means that if you run 128Mb of RAM instead of 1 Gb, you can execute 3.2 millon seconds.

It can use up to 10Gb of RAM.

## Languages

Supports Python, Node Js, Java, C#, Golang, Powershell, Ruby, and a few custom runtime API (community supported like Rust)

If your language is not supported, you can use the Lambda Container Image, which must implement the Lambda Runtime API. 

## Invocations
### Synchronous Invocation

The execution is inmediate. All error handling must happen client-side (retries, exponential backoff, etc).

Some services that invoke it synchronously are: 
- User invoked: [[ELB]], [[API Gateway]], [[CF]], [[S3]] Batch replication
- Service Invoked: [[Cognito]], [[Step Functions]]
- Other Services: [[Lex]], [[Alexa]], [[KDF]]

An [[ALB]] also runs lambda synchronously. This allows to expose the lambda as a https endpoint. The ALB gives you a [[NS]] , and the function is registered as a target group.

Each request for a lambda is received as JSON:
```
requestInformation: elb:targetGroupARN
httpMethod: GET
path: /lambda
queryStringParameters: {param1:1, param2:2}
headers: {
	connection: keep-alive
	host: lambda_alb_123.us_east_2.elb.amazon.aws.com
	user-agent: Mozilla...
	x-amazn-trace-id: "root=..."// for x-ray
	x-forwarded-for: 43.23.123.53,
	x-forwarded-proto: http,
},
body:"",
isBase64Encoded: false
```

The response is also JSON:

```
statusCode: 200,
statusDescription: "200 OK"
headers: {
	"Content-Type: "text/html", charset-utf8
},
body: <h1>Helloword</h1>
isBase64Encoded:false
```

#### ALB Multiheader values
I don't really get the use of this. Mutliple values for the same parameter. Perhaps to receive an array of values?

### Lambda Edge

The lambda can be deployed to [[CF]]. That allows you to customize the CDN content. Lambda can dynamically change CloudFront requests and responses before and after sending and receiving. You could, for instance, use it to pull data from [[DynamoDB]] and put the data in the page output, directly in the CDN.

Ideas to use it:
- Website security and privacy
- SEO
- A/B testing
- User priorization
- User tracking 
- Dynamic web apps at the edge
- Intelligently Route Across Origins and Data Centers
- Real time image transformation
- User auth

### Async invocations

All async invocations are done thorugh a [[SQS]]. On an error, Lambda will retry 3 times, 1 minute after the first attempt, two minutes after the second. 
As you have these retries, you have to be sure that the process is idempotent. 
A [[DLQ]] can be setup for fails.

Some services that call Lambda asynchronously are:
- [[S3]]
- [[SNS]] 
- [[CW]]
For non versioned S3 buckets, if two almost simultaneous requests are done to write an object (same prefix: same name/key) it can happen that only a single notif is sent. To garantee multiple requests on every write operation, use versioning in S3.

An interesting pattern is, to save files to [[S3]] and then use [[DynamoDB]] as a metadata store for the files. This metadata can be written using lambda.

### Streams and Lambda
![[streams_to_lambda.png]]
[[KDS]] here is receibing data, with hashing, separating it to shards, which batches them and then Lambda pulls out the data from those batches with an invoker, perhaps something using the [[KCL]].

Two important concepts here are the batch window, the time to gather records before the batch is sent, and the batch size: the max amount of items per batch. 
When there are not so many events, this architecture allows for the batches to be filled and in this way lambda's executions are more efficient than if you run it one event at a time. 
If the function returns an error, the entire batch will be reprocessed until success or until items get terminated from the shard. Processing is paused until the error is resolved, in the affected shard.

To solve this, events can be discarded, sent to a destination, the amount of retrials for a batch can be restricted, or the batch can be split into smaller batches. When creating a lambda, a timeout is specified. It could be that the batch is too big to process under the stipulated time, but a smaller batch would work. Remember that, after all, a lambda has a determined timeout.

You can only have one lambda per stream shard, so it pays to have a good hashing key.

A similar thing, stream processing, can be done from a [[SQS]]. The lambda polls the SQS. It will use Long Polling, which means that the lambda can wait for a bit before getting an answer, as to allow the SQS to receive events.
![[streaming_sqs.png]]

When using FIFO queues, lambda scales to the amount of active messages groups in the queue. Here, scaling means running multiple lambdas for each message group.
When using non FIFO, scaling just processes the items as fast as possible. 60 new lambda instances can be scaled up per minuet, and up to 1000 batches can be processed simultaneously.

If there is an error, the batches are returned to the original queue as individual items and they could end up with different grouping (different batch grouping or different MessageGroupId). Even if there is no error, it could happen that the event mapping gets the same element twice. 


### Destinations

![[destinatinos.png]]

[[SQS]], [[SNS]], another Lambda and [[EventBridge]] can all be destinations after success or failure. Destinations are recommended instead of DLQ's.

### [[IAM Role]] for Lambda

There are sample managed policies, such as:
- Lambda Basic Execution Role: uploads logs to [[CW]]
... Expand later

Ideally, one would have one Lambda Execution Role per function.
Be mindful that when using Event Source Mapping to invoke lambda, it uses the IAM Role to read event data.

### Lambda [[Resource Based Policies]]

They give other accounts permissions to use Lambda Resources. A [[IAM Principal]] can use the lambda if:
- the [[IAM Policy]] attached to the principal authorizes the principal to access it.
- If the resource based policy authoirzes it.
TODO: add examples here

### Lambda Envvars
Envvars can be stored and also encrypted with [[KMS]]. The max size is 4kb.

### [[CW]] Metrics

TODO

### [[X-Ray]]
TODO

### [[VPC]] invocation
![[VPC Lambda invocation.png]]The lambda, inside a [[VPC]], requires to access to a [[NAT]] which accesses a [[IGW]] . The lambda can access the [[DynamoDB]] tat is outside either the public or private cloud because of the Dynamo Endpoint.

### Config
#### RAM
128 Mb to 10Gb in 1Mb increments. Increasing RAM increases CPU. 1792Mb gives you 1 vCPU. However, your application needs to be multithreaded to benefit from multiple CPU.

#### Timeout
Default 3 seconds,max 900 seconds (15 min)

## Lambda Execution Context

This is a temporary runtime environment that initializes any external dependencies of your Lambda. Includes the /tmp directory. It is maintained for some time after execution, just in case that it can be reused, thus saving time.

This /tmp folder max size is 10Gb. Post execution, the folder remains and it can be used as a sort of a transient cache.

### Concurrency and throttling

You can have max 1000 concurrent executions (you can request more via ticket) of all your Lambdas. You can set a limit for each function as well, something called *reserved concurrency*. Each invocation after the reserved concurrency is met, causes a throttle.

In synchronous invocation, a throttle will result in a 429: Too many requests. In asynchronous invocation, it will retry automatically and it can go eventually to the DLQ. 

If you don't set up a reserved concurrency, one of your lambdas can block all the others.

### Cold starts and provisioned concurrency

When a lambda starts and there is no environment ready for it, it has to prepare the environment, creating all imports in the code. This means that the first request has a higher latency than the rest.

Later, as the lambda has a env ready to go, the calls take less time. If you wanted to reduce the cold starts, you can use *provisioned concurrency*. This allocates some environments in advance. This value can be auto scaled with a schedule or with target utilization values.

Provisioned concurrency is always less than reserved concurrency.

A way to reduce call startup time is to use Lambda Layers, in this way, you already have the modules without installation. As a minor tip, you don't need to install boto, it comes already in the AWS SDK.

### Lambda and [[Cloudformation]]
TODO: write the SAM

CF can create the Lambda dynamically for you, either with the code or taking it from [[S3]]. Be mindful that CF needs the correct execution roles to be able to access to the S3 and also that the resource policy of the bucket needs to authorize it too.

### Docker container images
There are a few existing docker images that you can use, or otherwise you can create your own (I have done this to deploy Tesseract). If you make your own, you have to be sure that they comply with the Lambda runtime API. You can do this automatically.

![[Pasted image 20230915184407.png]]

## Versioning
Lambdas are versioned, each publication creates an inmutable, increasing number. $LATEST is a reference, of course, to the latest. Each version has a different ARN, their own config and they are all accessible. 

## Alias
![[Pasted image 20230915184830.png]]

You can direct a certain porcentage of the traffic, allowing for canary testing, a/b testing, blue/green deployment, etc. Also each alias has its own ARN. An alias cannot point to another alias.

## Lambda and [[CodeDeploy]]

It helps to do the traffic shifting for the aliases, it is a feature already integrated in [[SAM]]. It can create up to 8 pre/post traffic hooks to check the health of the Lambda. The shifting can be done linearly, shifting x% per minute until reaching 100%, Canary, trying x% and then a 100%, and all at once.

## Monitoring
For [[CW]] Logs, the log retention policy is defined at the log group level, and not at the log stream level. 

To allow the Lambda to send data to [[X-Ray]], you could just create another IAM user, generate access keys and give them to the X-Ray agent to access. However, the best way is always to create the correct IAM roles and authorize the usage of the services. If you want X-Ray to write to the requests from a lambda of another account, you have to set said account as "trusted".