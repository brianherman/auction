# Auction - Alexa, Lambda, Signin with Amazon Tutorial
The original purpose of this project was to create a alexa app that would watch certain users on ebay and tell you how they were doing.
Unfortunately I cannot figure out how to get my app published so I decided to open source everything.
You need a aws account to run all the example code. This is non negotiable. You will also need 2 domain names through route 53 for the ssl certificates and hosting of static assets.

Auction alexa
this is a python application written in flask

#Prerequisites Windows
For this project you need to install python 3.6. You can download that from [here](https://www.python.org/ftp/python/3.6.4/python-3.6.4-amd64.exe).

# Prerequisites Ubuntu/Debian

    sudo apt-get update
    sudo apt-get install python3 python3-setuptools python3-dev

# Installation 
Next in a python command prompt.

    pip3 install -r requirements.txt

This will install all the dependencies it might take a while depending on your computer and internet connection.

# Zappa 
Zappa is the application that packages the lambda apps for aws lambda.
This is already installed in the requirements.
    
    cd auction_server 
    zappa deploy

This will deploy the server.

#AWS s3 and cloudfront

Now we have to setup the s3 buckets.
Create an s3 bucket with the name auction-<firstname>
this will create a s3 bucket for the assets.
Enable static web hosting on this bucket [get image for this].
Now use give SES permission to use the bucket for email.

    {
      "Version": "2012-10-17",
      "Id": "GiveSESPermissionToWriteEmail",
      "Statement": [
        {
          "Sid": "GiveSESPermissionToWriteEmail",
          "Effect": "Allow",
          "Principal": {
            "Service": "ses.amazonaws.com"
          },
          "Action": "s3:PutObject",
          "Resource": "arn:aws:s3:::auction-<firstname>/*",
          "Condition": {
            "StringEquals": {
              "aws:Referer": "<Your account id here>"
            }
          }
        }
      ]
    }

Now we have to create a cloudfront distribution that points to thie s3 bucket.
Create a new Web distribution and select your S3 bucket as Origin Domain Name. Select HTTPS Only for Viewer Protocol Policy.
[create image here]
3) Setup Route53 MX records using SES to forward emails to S3

Go to AWS SES and verify a new domain. Generate DKIM Settings as we

In the left navigation head to “Rule sets”, create a new one and a new “Rule”. Enter administrator@yourdomain.com to the receipients as this email address is used by default to receive SSL certificate domain verification emails.
In the bottom part of the rule settings define a S3 Rule to save incoming email to a ‘folder’ in your bucket.

4) Request a free SSL certificate using AWS Certificate Manager (former ACM)
Enter one or more domain names, you want to create a SSL certificate for. You can even use a wildcard.
Now you should have a new email on your S3 bucket containing the verification link. Download the email file open it in your favorite text editor and copy the verification link to your browser.
5) Assign the SSL certificate to your Cloudfront distribution

You’re almost done. Go back to Cloudfront and edit your distribution. Now you should be able to select your brand new SSL certificate.


# References
https://medium.com/@sbuckpesch/setup-aws-s3-static-website-hosting-using-ssl-acm-34d41d32e394