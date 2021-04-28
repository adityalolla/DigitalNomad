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

<img src="https://user-images.githubusercontent.com/81785727/116139869-5dd77b80-a68b-11eb-87c0-aa2d76d0d138.png" width="500" height="250">

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

1. Open Route53 on AWS console 
2. Find and register an available domain -> This will ask you for contact details, fill them out and usually takes about 4 hours for the domain to be available 
3. For each domain that you own, AWS will automatically create a hosted zone for you. 
4. Navigate to the hosted zone created for the new domain. You will already see 2 default entries for NS (name sever) and SOA (Start of authority)
5. These are created by default from AWS when you register a new domain via Route53 
6. We will need to create 2 more records for your hosted zone adityalolla.com 
7. Create record via simple routing protocol : First record leave the record name empty and select the route traffic to S3 in the search box, choose the region and select the bucket name adityalolla.com resource 
8. Likewise, for the second record under the record name give www 
9. For this record select the bucket www.adityalolla.com 
10. You should have a total of 4 records for your hosted zone once you complete 

We are using two buckets here : the root will be adityalolla.com and we will use the re-direction bucket as www.adityalolla.com ; this means when someone types www.adityalolla.com , the second bucket will re-direct requests to the root. 

```
If both steps are completed successfully; adityalolla.com should load your website for you on the browser 
``` 

### Setting up SSL Cert with ACM : 

1. Request a public certificate 
2. Under domain names add : adityalolla.com and www.adityalolla.com 
3. It will create CNAME records and values and also gives you the option to automatically add them to route 53, click on that option 
4. For both domains it will add one CNAME record in Route 53 

Wait for the public cert to be available. 


### Setting up a Cloudfront distribution 

We will be creating 2 Cloudfront distributions : One for the source index.html and the other distribution for re-direction 

You will need the source bucket adityalolla.com s3://uri which can be collected from the S3 bucket properties. Enter this WITHOUT the http:// 
In this case it is : adityalolla.com.s3-website-us-west-1.amazonaws.com ; This will be the Cross origin domain name for the first distribution 

##### Set the following : 
1. Viewer protocol policy : Redirect http to https 
2. SSL Certificate : Choose the certificate name from the previous step 
3. Alternate domain name : adityalolla.com (Likewise use www.adityalolla.com when you create the second distribution)  
4. Wait for the distribution status to change to : Deployed 

### Route 53 : 

We will need to edit the route 53 record sets to point to our newly created cloudfront distributions and not to our source S3 buckets 

1. Delete the A records for the S3 buckets 
2. Create a new record with Simple routing and A record 
3. Leave the record name empty for adityalolla.com and enter www when creating the second A record 
4. Under route traffic to : Choose : Alias to cloudfront distribution 
5. Take the domain name of the cloudfront distribution (You can see this by going to the Cloudfront distribution) 
6. For now you can set the evaluate heakth of target to NO 

In total now your Route 53 Hosted zone will contain 5 entries : 

Name servers, CNAME for your ACM cert, 2 A records for adityalolla.com and www.adityalolla.com and 1 Start of Authority SOA for your DNS




