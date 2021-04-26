# DigitalNomad
Project to host website on S3, setup CDN with AWS CloudFront, interactive visitor count with DynamoDB and AWS Lambda. 
Establish CI/CD Pipeline with build automation tests, deploy the stack using IaC 

## Static website hosting with S3 

Project breakdown : 

* Create a resume website using HTML5
* Use CSS to style the site 
* Host the site using AWS S3 
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

### Create a website using HTML5/CSS 

This project uses a bootstrap project with pre defined index.html. Changes are done to reflect personalizing the html content to your website. 
Directory structure follows index.html, css, js and src/img

The CI Pipeline will be used further to make all changes to the html code 

### Host the site using S3 

##### Pre-requisites 

This project requires an AWS account with awscli configured with an IAM role access id and secret key which will have permissions to the needed AWS services : 
S3, Cloudfront, Lambda 

```
aws --version
aws-cli/2.1.36 Python/3.8.8 Darwin/19.6.0 exe/x86_64 prompt/off
```
#### S3 Configurations : 

1. Create 2 S3 buckets : Name each of this bucket : domain.com and www.domain.com , in this example : adityalolla.com and www.adityalolla.com 
2. De-select the block public access when creating the buckets. Note : All contents of the bucket adityalolla.com will can be read by anyone publically 
3. Add a bucket policy to make it public, so that we do not have to keep editing the permissions with each object upload. 
4. Note that we are only providing the index.html and not the error.html, AWS will display the default error static page if our website is down or can't be reached.
5. Once the buckets are created, click on the buckets and enable : Static website hosting 
6. Specify the index document as index.html 
7. Save the changes  

<img src="https://user-images.githubusercontent.com/81785727/116139869-5dd77b80-a68b-11eb-87c0-aa2d76d0d138.png" width="100" height="100">

#### S3 Bucket policy :

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::adityalolla.com/*"
        }
    ]
}
```

##### Adding the bucket policy : 

```
aws s3api put-bucket-policy --bucket adityalolla.com --policy file:///Users/Aditya/Desktop/policy.json 
```

##### Uploading contents of static website to s3 bucket 

```
aws s3 cp startbootstrap-resume-gh-pages s3://adityalolla.com --recursive
upload: startbootstrap-resume-gh-pages/index.html to s3://adityalolla.com/index.html
upload: startbootstrap-resume-gh-pages/assets/img/favicon.ico to s3://adityalolla.com/assets/img/favicon.ico
upload: startbootstrap-resume-gh-pages/js/scripts.js to s3://adityalolla.com/js/scripts.js
upload: startbootstrap-resume-gh-pages/css/styles.css to s3://adityalolla.com/css/styles.css
upload: startbootstrap-resume-gh-pages/assets/img/profile1.jpg to s3://adityalolla.com/assets/img/profile1.jpg
upload: startbootstrap-resume-gh-pages/assets/img/profile.jpg to s3://adityalolla.com/assets/img/profile.jpg
```

### Buy a custom domain name and set up the DNS using AWS Route 53 

We will use Route53 for buying the custom domain, creating records for IPv4 and IPv6 using Simple routing and also setting up health checks with Route53. 


