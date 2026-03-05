# Host a Static Website on AWS using S3 and CloudFront

## Objective

Deploy a static website on AWS using:

-   Amazon S3 -- store website files
-   Amazon CloudFront -- deliver content globally using CDN
-   Route53 (optional) -- configure custom domain

This setup provides a serverless, scalable, and cost‑effective hosting
solution.

------------------------------------------------------------------------

# Architecture

User → CloudFront (CDN) → S3 Bucket → Static Website Files

------------------------------------------------------------------------

# Prerequisites

Ensure you have:

-   AWS Account
-   IAM user with permissions for S3 and CloudFront
-   AWS CLI installed
-   Cloned GitHub Project repo URL: https://github.com/vineethsankre/AWS_Codepipeline_S3_CloudFront.git

Install AWS CLI:

``` bash
pip install awscli
```

Configure credentials:

``` bash
aws configure
```

Prepare static files:

    website/
    ├── index.html
    ├── error.html
    ├── css/
    ├── js/
    └── images/

For React apps:

``` bash
npm run build
```

------------------------------------------------------------------------

# Step 1: Create an S3 Bucket

1.  Go to AWS Console → S3
2.  Click Create bucket

Configuration:

-   Bucket name: my-static-site-bucket
-   Region: select preferred region
-   Disable Block all public access

Create the bucket.

------------------------------------------------------------------------

# Step 2: Enable Static Website Hosting

Inside bucket:

Properties → Static website hosting

Configuration:

Index document: index.html

Error document: error.html

Save changes.

------------------------------------------------------------------------

# Step 3: Configure Bucket Policy

Go to:

Permissions → Bucket Policy

Add:

``` json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetObject"],
      "Resource": "arn:aws:s3:::my-static-site-bucket/*"
    }
  ]
}
```

Replace bucket name accordingly.

------------------------------------------------------------------------

# Step 4: Upload Website Files

Upload via console or CLI.

CLI command:

``` bash
aws s3 sync ./website s3://my-static-site-bucket
```

Verify S3 website endpoint.

------------------------------------------------------------------------

# Step 5: Create CloudFront Distribution

1.  Go to CloudFront → Create distribution

Origin:

S3 bucket

Settings:

Viewer protocol policy: Redirect HTTP to HTTPS

Default root object:

index.html

Create distribution and wait for deployment.

------------------------------------------------------------------------

# Step 6: Access the Website

CloudFront provides a domain like:

https://dxxxxxx.cloudfront.net

Open it in a browser.

------------------------------------------------------------------------

# Step 7 (Optional): Custom Domain with Route53

1.  Create hosted zone
2.  Create A record
3.  Select Alias → CloudFront distribution

Example:

www.example.com → CloudFront

Add SSL certificate using AWS Certificate Manager.

------------------------------------------------------------------------

# Cache Invalidation

After updating files:

``` bash
aws cloudfront create-invalidation --distribution-id DISTRIBUTION_ID --paths "/*"
```

------------------------------------------------------------------------

# Expected Result

Your static website will be accessible via:

-   CloudFront URL
-   Custom domain (optional)

Features:

-   Global CDN delivery
-   HTTPS enabled
-   Scalable serverless hosting

------------------------------------------------------------------------

# Cleanup

To avoid charges:

1.  Delete CloudFront distribution
2.  Delete S3 bucket

