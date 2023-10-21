#AWS 
Destinations include S3, RDS, DynamoDB, Redshift, and EMR.
Manages task dependencies, retries and notifies failures, Data sources can be on-premise, and it is highly available.

![[Pasted image 20231007172415.png]]

Difference with [[Glue]] :
- In Glue you dont worry about the resources
- In Pipeline, this is an orchestrating service. You can actually access to the machines being created.