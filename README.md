# DigitalNomad
App packages for online resume 

## Static website hosting with S3 

Project breakdown : 

* Create a resume website using HTML - index.html -> Ready
* Use CSS to style the site - In progress
* Host the site using AWS S3 - Done
* Use HTTPS for security, using an AWS CloudFront distribution
* Buy a custom domain name and set up the DNS using AWS Route 53
* Write some JavaScript to create a hit counter for the site
* Set up a DynamoDB database to store the hit count
* Build an API & an AWS Lambda function that will communicate between the website and the database
* Write the Lambda function in Python
* Write and implement Python tests
* Deploy the stack by using infrastructure-as-code, not by clicking around in the AWS console
* Use source control for the site via GitHub
* Implement automated CI/CD for the front end
* Implement automated CI/CD for the back end

##### Create a website using HTML5/CSS 

This project uses a bootstrap project with pre defined index.html. Changes are done to reflect personalizing the html content to your website. 
Directory structure follows index.html, css, js and src/img

The CI Pipeline will be used further to make all changes to the html code 

##### Host the site using S3 

###### Pre-requisites 

This project requires an AWS account with awscli configured with an IAM role access id and secret key which will have permissions to the needed AWS services : 
S3, Cloudfront, Lambda 

'''
aws --version
aws-cli/2.1.36 Python/3.8.8 Darwin/19.6.0 exe/x86_64 prompt/off
'''

1. Create 2 S3 buckets : Name each of this bucket : domain.com and www.domain.com , in this example : adityalolla.com and www.adityalolla.com 
2. De-select the block public access when creating the buckets. Note : All contents of the bucket adityalolla.com will can be read by anyone publically 
3. Add a bucket policy to make it public, so that we do not have to keep editing the permissions with each object upload. 
