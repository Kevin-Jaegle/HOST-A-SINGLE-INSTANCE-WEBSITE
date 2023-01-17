# Host-a-single-Instance-Website

In this project I will first deploy the VPC using CloudFormation template, then create an database on Amazon RDS, also launch an EC2 instance. After that, we will set up the connection between RDS and EC2, then install the WordPress website on the instance. Finally, we will start hosting a simple WordPress website on EC2 instance and export the static assets into S3 bucket.



![Project-Architecture](https://user-images.githubusercontent.com/122367884/212283346-7e4536e5-a0ea-4e35-bd4b-b7e0a3226049.jpeg)

## Step 1. Create a VPC CloudFormation Stack

* I used a CloudFormation template
* In the CloudFormation console I choosed a new stack with uploading the CloudFormation template and the setting "With new resources (standard)" 

![Create Stack](https://user-images.githubusercontent.com/122367884/212288089-d29c9527-2f67-4da6-95bc-87998b2e92ab.jpg)

* I used vpc-stack for Stack name
* For Number of Availability Zones, I selected 2 and choose us-east1a and us-east1b
* I left the other settings on default and created the stack

![Stack-detail](https://user-images.githubusercontent.com/122367884/212289465-99a92050-1d26-4e19-8e7a-290e61fe65a9.jpg)

## Step 2. Create a RDS MYSQL Database

* Create a new Database
* For Egine options I used MySQL
* For Templates, Free tier
* In the Settings section, the DB identifier i gave the name wordpress
* Below Credentials Settings, I entered a random Master username an Master password (for example: admin123, #12345678aA)

![DB Einstellungen](https://user-images.githubusercontent.com/122367884/212841212-7d9a758e-fd57-4486-a65e-7ed333f097d0.jpg)

![Free Tier](https://user-images.githubusercontent.com/122367884/212841486-fd0175d8-ab2b-4d67-a0a2-2ce1f7499011.jpg)

![Username and Password](https://user-images.githubusercontent.com/122367884/212841632-7cb85c35-ad6b-49b4-8cc8-ff9a50fee9da.jpg)

###Now I continue in the Connectivity section

* for the Virtual Private Cloud (VPC), I selected the Vpc / vpc-stack option, i.e. the VPC created via CloudFormation in the previous step.
* I am creating a new VPC Security Group
* as name for the security group i choose db-sg

![Security group](https://user-images.githubusercontent.com/122367884/212842764-10aab38c-a79c-4f77-8eb6-6057fa2db87d.jpg)

* I call the initial database wordpress and create the database

![initial database](https://user-images.githubusercontent.com/122367884/212843231-a6dc9f46-5154-4ab9-8b7c-854dad099ac8.jpg)

## Step 3. Launch a EC2 Instance

* I create a new Instance with the name wordpress
* As operating system images (Amazon Machine Image): I choose Amazon Linux (should be preselected)
* Instance type: t2.micro (should also be preselected) Note: Care should be taken here to only use instance types that are marked as "Free tier eligible"   to avoid additional charges to your AWS account.

![EC2 instance](https://user-images.githubusercontent.com/122367884/212844900-a665550f-c551-48db-9086-b02771f5a3dc.jpg)

