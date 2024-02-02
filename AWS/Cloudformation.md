#AWS 
In most templates for most object types, there is a property called `DeletionPolicy`. If it is marked as Retain, deleting the CF stack will not delete the object. Otherwise, it will be Deleted. 

## CLI

`aws cloudformation describe-stacks`

`aws cloudformation delete-stack --stack-name your-stack-name`

## Template tips:

- A vertical bar | generates a multiline string for everything that is indented under it. 

### EC2 Userdata 

Combining Fn::Base64, Fn::Sub and Fn::ImportValue
```
EC2Instance:
	Properties:
		UserData:
			Fn::Base64:
				Fn::Sub:
				- | # this is to make a multilinescript
				  #!/bin/bash
				  echo {ValueImportedFromOtherStack}
				- ValueImportedFromOtherStack:
				  Fn::ImportValue: actual-name-reference
```


## Extra resources

- https://garbe.io/blog/2017/07/17/cloudformation-hacks/