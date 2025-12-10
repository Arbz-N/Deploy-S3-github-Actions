_**Portfolio Deployment in S3 via GitHub Actions**_

    This project demonstrates how to automatically deploy a static portfolio website to an AWS S3 bucket using GitHub Actions.
    Whenever you push changes to the main branch, the workflow automatically syncs your website files to S3 and makes it publicly accessible.

**Prerequisites**

    Make sure you have:
    A GitHub repository with your website files (HTML, CSS, JS, images, etc.)
    An AWS account with permissions to create/use S3 buckets
    GitHub Secrets configured with your AWS credentials:
    
    AWS_ACCESS_KEY_ID
    AWS_SECRET_ACCESS_KEY

    Go to your GitHub repository → Settings → Secrets → Actions and add the AWS credentials.
    These will be used by the workflow.

**AWS S3 Setup**

    Create an S3 bucket:
    
    Bucket name: <your-s3-bucket-name>
    Region: us-east-1 (or your preferred region)
    
    Enable static website hosting & allow public access:
    
    Index document: index.html
    Error document: index.html (or error.html)

    Set bucket policy for public access:

        {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Sid": "PublicReadGetObject",
                    "Effect": "Allow",
                    "Principal": "*",
                    "Action": "s3:GetObject",
                    "Resource": "arn:aws:s3:::<your-s3-bucket-name>/*"
                }
            ]
        }

**GitHub Actions Workflow**

    Create a workflow file in your repository:
    
    .github/workflows/deploy.yml
    
    Paste the following workflow:
    
    name: Portfolio Deployment in S3 via Github-Action

    on:
      push:
        branches:
          - main
    
    jobs:
      build_deploy:
        runs-on: ubuntu-latest
        steps:
          # Step 1: Checkout repository
          - name: Checkout
            uses: actions/checkout@v1
    
          # Step 2: Configure AWS credentials
          - name: Configure AWS Credentials
            uses: aws-actions/configure-aws-credentials@v1
            with:
              aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
              aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
              aws-region: us-east-1
    
          # Step 3: Deploy static site to S3 bucket
          - name: Deploy static site to S3 bucket
            run: aws s3 sync . s3://<your-s3-bucket-name> --delete


    Replace <your-s3-bucket-name> with your actual S3 bucket name.
    Make sure GitHub Secrets are correctly added, otherwise the workflow will fail.

**Test Your Deployment**
    
    After pushing changes to your main branch:
    Open your S3 website endpoint in a browser:
    
    http://<your-s3-bucket-name>.s3-website-us-east-1.amazonaws.com

    You should see your portfolio website live.

**Notes**

    Read each file carefully before executing.
    This workflow overwrites the contents of your S3 bucket on each push.
    Delete the bucket if deploying only for practice to avoid unnecessary AWS charges.