# SIMPLE S3 STATIC Website

This is a simple template for static website hosted on AWS using GitHub Actions.

### Things you will learn to use
- GitHub Actions
- AWS Services (S3, Route53, IAM)

## Part 1 (Create S3 Bucket)
1. On your AWS account, create an s3 bucket with the following conditions:
- Object Ownership: `ACL Enabled`
- Block Public Access settings for this bucket: uncheck `Block all public access`
- Then click "Create Bucket"
- After Creation, go to `Properties`, and enable `Static website hosting`, on Index and error document, you can put `index.html`
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
- Step 2: Add Statement(s)- "Effect"=`Allow`, "Principa"=`*`, "AWS Service"=`Amazon S3`, "Actions"=`All Actions`, "Amazon Resource Name (ARN)"=you can find this on your bucket.
- Note: on ARN, always input `/*` at the end ARN.
- Step 3: Generate Policy
- Step 4: Copy and paste this to the bucket policy.


## Part 2 (Create IAM for S3 Bucket)
1. On your AWS account, go to IAM, under User, click "Add users", with the following parameters:
- User name = (up to you)
- Select AWS credential type = `Access key - Programmatic access`
- Click "Attach existing policies directly"
- Search and choose `AmazonS3FullAccess`
- You can skip tags, and go straight to "Review"
- Click "Create user", and take note of the details, you will need them soon for GitHub Actions
```
AWS_S3_BUCKET = Bucket Name
AWS_ACCESS_KEY_ID = Access Key ID
AWS_SECRET_ACCESS_KEY = Secret Access Key
```

## Part 3 (GitHub)
- You can fork or clone this repository, and ensure to update the Secrets with the details above. You can edit this by going to your Repository > Setting > Secrets > Actions.
- Or a new repository with instructions below
1. Click "+" sign on upper left and click "New repository", enter your repositor name and click create.
2. Clone your repository to your desktop, by running the following command:
```
git clone https://github.com/Heprex/test-static.git
```
- take note of your git web url, which you can find on your repository under "Code"
3. After cloning, navigate to the folder on your terminal, this is to ensure that we are on the right working directory when commiting or pushing changes to github.
4. On VSCode, open your folder that you have cloned and create a folder named `.github/workflows`
5. On that folder, create a `main.yaml` file and paste the following details:
```
name: Upload Website

on:
  push:
    branches:
    - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    - uses: jakejarvis/s3-sync-action@master
      with:
        args: --acl public-read --follow-symlinks --delete  --exclude '.git*/*' --exclude 'README.md'
      env:
        AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```
What this does is called `S3 Sync` to sync a directory with a remote S3 bucket, kudos to [jakejarvis](https://wweb.dev/blog/how-to-create-static-website-npm-scripts), more details here:
- https://github.com/jakejarvis/s3-sync-action
- https://github.com/marketplace/actions/s3-sync

6. On the very top folder, create a simple `HTML` file, with the below contents, and of course you can edit it to your desire:
```
<!DOCTYPE html>
<html>
    <body>

        <h1>This is Jeffrex's Test Website</h1>
        <p>Let the fun begins. -version 12</p>

    </body>
</html>
```
7. Refer to my repository for folder/file structure, and always make sure that you have the right bucket and secrets details on your repository.
8. After all files has been created locally, it's time to push it to your github repository. Using terminal, navigate to your folder and run the following commands:
```
git add . 
git commit -m "initial commit"
git push
```
9. Go back to your repo, refresh it, and you should see that your files should now be there.
10. You can also navigate to `Actions` tab and there, you will see that your files is being uploaded your S3 bucket, if errors seen, refer to the documentation mentioned above, and always check if your Secrets are properly configured.
11. If successful, you should see the files in your S3 bucket.
12. Get the `website endpoint` under `Properties` on your S3 bucket to see your site. Sample Below:
```
http://heprex.net.s3-website-ap-southeast-2.amazonaws.com
```

## Bonus (Route53)
1. If you want your s3 hosted static website to have a domain, use route53 or other domain providers.
2. `IMPORTANT`: If you want to proceed with this, make sure that your `bucket name` is the `same` as your `domain name` to have this successfully working.
3. If you are using Route53, it's easy as creating a simple `A record`
4. Go to your domain name, and click `Create Record`, and use the following selections:
```
Record type = "A - Routes traffic to an IPv4 address and some AWS resources"
Value = tick to Alias --> Alias to S3 website endpoint --> Choose Region where the bucket is located --> You should see your S3 endpoint on dropdown menu
```
5. Then your domain will now point to the S3 endpoint created above.

## Other Options and future improvements 
1. by using Github Pages, same function for static websites.
2. Deploying via websever
3. Using cloudfront and other AWS services.

## To access my website
```
http://heprex.net
or
http://heprex.net.s3-website-ap-southeast-2.amazonaws.com
```