# Using Roles for External Accounts to Access AWS Resources

Example account 000000001 needs access to an s3 bucket on account 000000002

Follow section for creating the new [role](http://docs.aws.amazon.com/IAM/latest/UserGuide/walkthru_cross-account-with-roles.html)

Then from the other account you will be able to switch to the role provided in account 0000002 and recieve temp creds to access 
required resources

"Switching Roles with the AWS CLI" in [docs](http://docs.aws.amazon.com/IAM/latest/UserGuide/walkthru_cross-account-with-roles.html)

Using `aws cli` for this type of access is painful... you will have to deal with the temp access variables and export them in the cli.

AWS STS AssumeRole is best handled in AWS SDKs, example here in Python Boto

```python
import boto3

sts_client = boto3.client('sts')

assumedRoleObject = sts_client.assume_role(
    RoleArn="arn:aws:iam::00000000001:role/name-of-new-role",
    RoleSessionName="AssumeRoleSessionZane"
)

#Temp creds
credentials = assumedRoleObject['Credentials']

s3_resource = boto3.resource(
    's3',
    aws_access_key_id = credentials['AccessKeyId'],
    aws_secret_access_key = credentials['SecretAccessKey'],
    aws_session_token = credentials['SessionToken'],
)

for bucket in s3_resource.buckets.all():
    print(bucket.name)
```
