# Easy and Effective Data Archiving with Terraform and AWS 

Continually sync data to s3 bucket using [AWS sync cli](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html) comes in handy when you want to archive data in s3

First setup the s3 bucket, backup user, and security access with [Terraform](https://terraform.io)

```terraform
## Autozane s3 bucket, user and access
resource "aws_s3_bucket" "autozane_backup" {
    bucket = "autozane_backup"
    acl    = "authenticated-read"
    region = "us-west-2"

    tags {
        Name = "AutoZane Backup"
    }
    lifecycle {
      prevent_destroy = true
    }
}

#IAM User
resource "aws_iam_user" "autozanebackup" {
    name = "autozanebackup"
    path = "/users/"

}

#IAM group
resource "aws_iam_group" "autozanebackup" {
    name = "autozanebackup"
    path = "/users/"
}

#membership
resource "aws_iam_group_membership" "autozanebackup" {
    name = "autozanebackup-group-membership"
    users = [
        "${aws_iam_user.autozanebackup.name}"
    ]
    group = "${aws_iam_group.autozanebackup.name}"
}

resource "aws_iam_policy" "autozanebackup" {
    name = "autozanebackup"
    description = "Autozane backup access"
    policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:ListBucket"],
      "Resource": ["arn:aws:s3:::autozanebackup"]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:DeleteObject",
        "s3:GetObject",
        "s3:PutObjectAcl"
      ],
      "Resource": ["arn:aws:s3:::autozanebackup/*", "arn:aws:s3:::autozanebackup/"]
    }
  ]
}
EOF
}

resource "aws_iam_policy_attachment" "autozanebackup-attach" {
    name = "autozanebackup-attachment"
    groups = ["${aws_iam_group.autozanebackup.name}"]
    policy_arn = "${aws_iam_policy.autozanebackup.arn}"
}
```

Create your access keys for autozane user outside of Terraform and use

```bash
aws configure
```

To securly input the new access keys. I recommmend leaving the access key creation outside of Terraform since the key will get saved in the 
state file which could be a bit of a security hazard if state file isn't handle properly.

At this point you are good to go for archiving

```bash
aws s3 sync /directory/to/archive/ s3://autozane 
```
