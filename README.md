# Host-a-single-Instance-Website

In this project I will first deploy the VPC, using CloudFormation template, then create an database on Amazon RDS, also launch an EC2 instance. After that, I will set up the connection between RDS and EC2, then install the WordPress website on the instance. Finally, I will start hosting a simple WordPress website on EC2 instance and export the static assets into S3 bucket.



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

#### Now I continue in the Connectivity section

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

* I create a new Key pair with the name wordpress

![Key Pair](https://user-images.githubusercontent.com/122367884/212848701-5e974f02-5ef0-49c6-bc92-50709526d58c.jpg)

* For VPC, I select "Vpc/vpc-stack" (the VPC we've created earlier)
* For Subnet, I choose "PublicSubnet0"
* For Sevurity group name and the description field, I enter public-instance-sg
* I then start the Instance

![Netzwerkeinstellungen](https://user-images.githubusercontent.com/122367884/212850354-3d7201bf-ac36-4637-b07f-c9dc845bf073.jpg)

## Step 4, Modify Security groups of RDS and EC2 Instance

#### In this section, I will change the Security groups of the EC2 instance and the MySQl database to ensure that both systems can communicate with each      other.

* I select the Security group public-instance-sg and Edit 2 new Inbound rules
* For Type I select MySQL/Aurora
* For Source I select db-sg and add the rule
* Then I add a new rule with the type HTTP 
* For Source I select db-sg and save the rules

* I select the Security group db-sg and Edit a new Inbound rule
* For Type I select MySQL/Aurora
* For Source I selct public-instance-sg and save the rule

## Step 5. Set up the wordpress environment

#### In this section, I will connect to the EC2 instance, install and configure Wordpress (and some prerequisites).

Note: In step 2, I could have just selected an already installed Wordpress image from the AWS Marketplace. That's also a good option, but here I'm going the manual installation route so that you understand the various steps involved in setting up a WordPress instance from scratch.

I Call up the EC2 console.
Select the instance with the name wordpress.
and connect to the EC2 instance via SSH.

After I successfully establish the SSH connection, I run the following commands to install Apache (web server) and MySQL on your instance:

![sudo install](https://user-images.githubusercontent.com/122367884/212864991-d331fc11-0e4d-4125-8cef-6121e7dc7827.jpg)

#### Now I set the endpoint of the database I created earlier as an environment variable so that the wordpress instance knows  where to connect to. The      endpoint can be found in the RDS console, where I select the WordPress database and then copy the value shown as the endpoint.

* The address is then set as an environment variable with the following command (I replace it with the value copied above)

![export](https://user-images.githubusercontent.com/122367884/212867297-87bc8970-62c6-4aa4-91a8-63f12604145b.jpg)

#### Next, I connect to the database using the username and master password I set earlier when I created the database. so I replace the placeholders with those.

![mysql](https://user-images.githubusercontent.com/122367884/212867833-8b2d1e9c-9e38-4e67-afba-7e4a0282f390.jpg)

#### I am now connected to the database (a welcome message should be seen). now I create a new database for wordpress and grant the necessary permissions: 

![berechtigungen](https://user-images.githubusercontent.com/122367884/212868414-8be1ae3b-dcf3-49d2-918c-5b42c6fa8d86.jpg)

#### Using the exit command above, I am now back on the EC2 instance. Now I download Wordpress and unzip it.

![wget](https://user-images.githubusercontent.com/122367884/212868755-8ad09076-d385-4541-b562-d453e5f0c185.jpg)

#### I change to the WordPress folder and save the default configuration file.

![cd wordpress](https://user-images.githubusercontent.com/122367884/212869100-5fd043fb-65a1-4c12-9f15-505a5f94db51.jpg)

#### Danach bearbeite ich die Datei wp-config.php mit nano:

![nano](https://user-images.githubusercontent.com/122367884/212869316-7c496bfd-b1cd-4fee-b91e-2c1e2c029e4a.jpg)

#### Now I replace the first three placeholder values with the following values:

* DB_NAME: 'wordpress'
* DB_USER: 'wordpress'
* DB_PASSWORD: 'wordpress-pass'

![placeholder](https://user-images.githubusercontent.com/122367884/212870001-a89aa13a-4bd7-4727-8750-d8464e4d644b.jpg)

#### For the database host, I use the endpoint address I copied earlier

* DB_HOST: '<replace-me>'
  
#### I visit [this link](https://api.wordpress.org/secret-key/1.1/salt/) which gives me unique keys and salt information for cryptographic operations of wordpress instance. Replace the above block with the information from the website.
  
![define](https://user-images.githubusercontent.com/122367884/212872156-06406ec8-13e4-4bb9-9739-44282ef5ba40.jpg)

#### then I copy the snippet below that configures a required plugin.
  
![W3tc](https://user-images.githubusercontent.com/122367884/212872277-d6c5a27b-d634-4f4a-a85d-2ce5cd5ee082.jpg)

#### Now that Wordpress is configured, I install a few more dependencies, copy the Wordpress files to the web server directory, and make sure the web        server (Apache) has the appropriate permissions for the Wordpress files.  
  
![sudo](https://user-images.githubusercontent.com/122367884/212873076-9e60cd0a-d3a5-4ac3-9174-db68f071aabf.jpg)

#### finally I start the web server.
  
![start](https://user-images.githubusercontent.com/122367884/212873693-2cc74866-d44d-49c3-b42a-9fd12588ab76.jpg)

## Step 6. Visit your Website and configure the Wordpress setting
  
#### In this section, I complete the WordPress installation

* First, I create a new user in AWS that will use the WordPress plugin for Amazon S3.
* I enter Wordpress as the user name.
* In the Select AWS access type section, I select Access key - Programmatic access.
* I check the Attach existing policies directly box at the top of the next page.
* I search for CloudFrontFullAccess and select it.
* I do the same for AmazonS3FullAccess.
* Now I open in the EC2 instances the Public IPv4 DNS
* On the setup page I choose a name for the website, a username, a password and my email and then click on Install WordPress

![wordpress](https://user-images.githubusercontent.com/122367884/212889962-edc3f2b3-6b7f-4622-b641-43b98405dadb.jpg)
  
#### After a few seconds I see the message Success! I click Log In to continue.
     I enter my username and password and click the Log In button to display the WordPress dashboard.
  

