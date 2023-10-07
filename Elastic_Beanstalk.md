Most apps will have an [[ALB]] and a [[ASG]] . You use components and pay for the underlying instances. An application is then a set of Elastic Beanstalk Components. All applications have:

- Version
- Environment: set of components running an application version
- Tiers: Worker and Webserver
- You can have multiple envs.

![[Pasted image 20230912195432.png]]

Supports Go, Java JE, Java with Tomcat, NetCore on Linux, Net for windows, Node.js, PHP, Python, Ruby, Docker Builder, Single Docker, Multiple Docker, Preconfigured Docker. You can create your own custom platform.

## Webserver Tier

We have a [[ELB]] that distributes load accross two availability zones, through which an [[ASG]] keeps [[EC2]] machines.
![[Pasted image 20230912195653.png]]

## Worker Environment
Here we have a set of [[EC2]] instances that are kept inside a [[ASG]] and they communicate through a [[SQS]] queue.

![[worker_environment_eb.png]]

The [[ASG]] grows/reduces depending the amount of messages inside the [[SQS]].