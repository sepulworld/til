# Use Bash Utilities to Update aws/credentials for AssumeRole

```bash
aws sts assume-role --role-arn <ROLEARN> --role-session-name <ROLESESSIONNAME> |\
    tr '{}' ',,' |\
    awk -F:  '
                    BEGIN { RS = "," ; print "[PROFILENAME]"}
                    /:/{ gsub(/"/, "", $2) }
                    /AccessKeyId/{ print "aws_access_key_id = " $2 }
                    /SecretAccessKey/{ print "aws_secret_access_key = " $2 }
                    /SessionToken/{ print "aws_session_token = " $2 }
    '  >> ~/.aws/credentials
```

OR if you don't want to touch your .aws/credentials file

```bash
aws sts assume-role --role-arn arn:aws:iam::1111111111111:role/role-test --role-session-name "RoleSessionTest" |\ 
    grep -w 'AccessKeyId\|SecretAccessKey\|SessionToken' |\ 
    awk  '{print $2}' | sed  's/\"//g;s/\,//' > awscre
    export AWS_ACCESS_KEY_ID=`sed -n '3p' awscre`
    export AWS_SECRET_ACCESS_KEY=`sed -n '1p' awscre`
    export AWS_SECURITY_TOKEN=`sed -n '2p' awscre`
```
