# SIMPLE S3 STATIC Website

This is a simple template for static website hosted on AWS using GitHub Actions.

### Things you will learn to use
- GitHub Actions
- AWS Services (S3, Route53, IAM)

## Part 1 (Create S3 Bucket)
1. On your AWS account, create an s3 bucket with the following conditions:
- Object Ownership: ACL Enabled
- Block Public Access settings for this bucket: uncheck "Block all public access"
- Then click "Create Bucket"
- After Creation, go to "Properties", and enable "Static website hosting", on Index and error document, you can put "index.html"
- After creation, go to your bucket, and under "Permission" tab, edit "Bucket policy", and you should have something ike this:
```
{
    "Version": "2012-10-17",
    "Id": "Policy1651577930481",
    "Statement": [
        {
            "Sid": "Stmt1651577928899",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:*",
            "Resource": "arn:aws:s3:::heprex.net/*"
        }
    ]
}
```
--Or you could use "AWS Policy Generator" with the following parameters:
- Step 1: Select Policy Type-  S3 Bucket Policy
- Step 2: Add Statement(s)- "Effect"=Allow, "Principa"=*, "AWS Service"=Amazon S3, "Actions"=All Actions, "Amazon Resource Name (ARN)"=you can find this on your bucket.
- Note: on ARN, always input "/*" at the end of the input.
- Step 3: Generate Policy
- Step 4: Copy and paste this to the bucket policy.


## Part 2 (Create IAM for S3 Bucket)
1. On your AWS account, go to IAM, under User, click "Add users", with the following parameters:
- User name = (up to you)
- Select AWS credential type = Access key - Programmatic access
- Click "Attach existing policies directly"
- Search and choose "AmazonS3FullAccess"
- You can skip tags, and go straight to "Review"
- Click "Create user", and take note of the details, you will need them soon for GitHub Actions
```
AWS_S3_BUCKET = Bucket Name
AWS_ACCESS_KEY_ID = Access Key ID
AWS_SECRET_ACCESS_KEY = Secret Access Key
```
