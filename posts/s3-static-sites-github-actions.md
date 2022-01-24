---
layout: post-layout.njk 
title: S3 Static Sites & GitHub Actions
date: 2021-11-18
tags: ['post']
---
<!-- Excerpt Start -->
This blog is now hosted from S3 and deployed automatically using GitHub Actions.
<!-- Excerpt End --> 

#### Netlify No More

I'd been hosting this site on Netlify without issue for some time. Netlify is a great static site
host especially for low traffic pages like this one. It works flawlessly and is free. So what's the problem?
Well, I think for me, Netlify was a little too "batteries included". You just point Netlify
to your repo and it does all the work for you when new code is pushed. That's great, but what if I want to do
the building an deploying myself!?

#### S3 Static Sites

Enter AWS S3. Now I'll say another motivation for me moving this site to S3 is that I like AWS. I recently got 
my Certified Solutions Architect cert, so I try to use AWS as much as I can. Considering S3 costs something like
$0.0025/GB, it's really not that different from the 'free' of Netlify. 

Pointing a domain name straight to an S3 bucket is a little slow, only supports HTTP, and the name of the S3 bucket
needs to match the domain. I found this is a little clunky and undesirable, so I did the 'proper' setup of creating a 
CloudFront distribution to sit in front of my S3 bucket. Now I can serve traffic over HTTPS, and my static content gets
cached at edge locations reducing networking costs. 

##### Route 53 

I decided to also go for the full AWS solution and also use Route 53 as my DNS service. This is actually the most
expensive part of the whole setup as AWS charge $0.50 a month per Hosted Zone. I think it is worth the convenience though. 
Your CloudFront distribution does not have a static IP, so you need to use its DNS record. This can present a challenge
when trying to point the zone apex for your domain to your CloudFront distribution since it is typically not possible to 
create a CNAME record for the zone apex. AWS gets around this with something called Alias records, which let you create
an A record at the zone apex which points to the CloudFront distribution, and I assume AWS does some magic under the hood
to make sure it's always pointing to the right IP. This is all to say, $0.50 for a Hosted Zone and using Route 53 makes
the job a lot easier.

##### How To 

Configuring an S3 bucket to serve a static site this way is not that difficult, so I don't want to write up all
the steps here when [excellent documentation](https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-serve-static-website/)
straight from the source already exists. You are going to want to follow the directions for using a website endpoint
as the CloudFront origin, otherwise the routing will be a little wonky (i.e. this page would only be reachable at
`/posts/3/index.html`)

#### GitHub Actions

While I could simply build my site with 11ty and drop the static files in my S3 bucket,
GitHub Actions are an awesome CI/CD tool for automating a small task like this. You just write
a YML file describing a workflow and GitHub does the rest:


{% raw %}
```
on:
  push:
    branches:
      - main
jobs:
  deploy:
    name: Upload to Amazon S3
    runs-on: ubuntu-latest
    # These permissions are needed to interact with GitHub's OIDC Token endpoint.
    permissions:
      id-token: write
      contents: read
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@master
        with:
          role-to-assume: ${{ secrets.IAM_ROLE }}
          aws-region: us-east-1

      - name: Install dependencies & build site
        run: |
          npm ci
          npm run build

      - name: Copy files to S3
        run: |
          aws s3 sync _site/ s3://${{ secrets.BUCKET_NAME }} --delete
```
{% endraw %}

When I push code to the `main` branch, GitHub executes the following actions in a VM:
- Checkout the code
- Assume an IAM Role in my AWS account that has permission to write to the S3 bucket
- Build my site with 11ty
- Use the AWS CLI to sync the S3 bucket contents with my new site contents

The way GitHub is able to authentic to my AWS account and assume that IAM Role is through 
OpenID Connect. You can read about configuring OpenID Connect between GitHub and AWS
[here](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services).
The easiest way to get started is to check out the sample [CloudFormation template provided by AWS](https://github.com/aws-actions/configure-aws-credentials#sample-iam-role-cloudformation-template)
which can set everything up for you and create a shell IAM Role which you just need to add the S3 permissions to.

#### Final Thoughts

*Wait, isn't this a lot more complicated than Netlify? And a lot less free?* Well, yes. The main
advantage is that CloudFront is going to serve your static site a lot faster around the globe than Netlify will.
For a small blog like this, that doesn't really matter. There is really no practical reason to serve the site this way, 
but like I said, I enjoy using AWS. I learned a few things about S3, CloudFront, Route 53, and GitHub Actions, and I feel more in control
and knowledgeable about the whole process. To me, that's worth it even though Netlify will do all the work for me. After all, isn't that why we're engineers?
