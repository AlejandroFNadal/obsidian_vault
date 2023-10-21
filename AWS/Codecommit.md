Fully managed, highly available git repository.  Can be accessed over SSH keys, HTTPS ([[IAM]]) or CLI Credential Helper. 

Using [[IAM Policy]] you can manage the permissions that the users and roles have to access the repositories. To allow for cross account access, use an [[IAM Role]] in your AWS account and use [[STS]] to assume roles.


It can be encrypted at rest using [[KMS]] or encrypted in transit. 