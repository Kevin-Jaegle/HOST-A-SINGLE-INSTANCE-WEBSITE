# Host-a-single-Instance-Website

In this project I will first deploy the VPC using CloudFormation template, then create an database on Amazon RDS, also launch an EC2 instance. After that, we will set up the connection between RDS and EC2, then install the WordPress website on the instance. Finally, we will start hosting a simple WordPress website on EC2 instance and export the static assets into S3 bucket.



![Project-Architecture](https://user-images.githubusercontent.com/122367884/212283346-7e4536e5-a0ea-4e35-bd4b-b7e0a3226049.jpeg)

## Step 1. Create a VPC CloudFormation Stack

* I used a CloudFormation template
* In the CloudFormation console I choose a new stack with uploading the CloudFormation template and the setting "With new resources (standard)" 

![Create Stack](https://user-images.githubusercontent.com/122367884/212288089-d29c9527-2f67-4da6-95bc-87998b2e92ab.jpg)

* I used vpc-stack for Stack name
* For Number of Availability Zones, I selected 2 and choose us-east1a and us-east1b
* I left the other settings on default and created the stack

![Stack-detail](https://user-images.githubusercontent.com/122367884/212289465-99a92050-1d26-4e19-8e7a-290e61fe65a9.jpg)

## Step 2. Create a RDS MYSQL Database
