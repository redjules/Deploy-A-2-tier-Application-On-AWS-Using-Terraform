# 🚀 Challenge 3 as part of #10WeeksofCloudOps - 2 tier Application using terraform

✨This repository is created to learn and deploy a 2-tier application on aws cloud through Terraform.

## 🏠 Architecture

![Architecture diagram](https://github.com/AnkitJodhani/3rdWeekofCloudOps/blob/main/architecture.gif)

[Image Source: Ankit Jodhani](https://github.com/AnkitJodhani/3rdWeekofCloudOps/blob/main/architecture.gif)

## 🖥️ Installation of Terraform

**Note**: Follow the blog for the step-by-step instructions to build this project. [Terraform](https://ankitjodhani.hashnode.dev/implementing-two-tier-architecture-in-aws-with-terraform-step-by-step-guide-10weeksofcloudops)

### Create S3 Backend Bucket

Create an S3 bucket to store the .tfstate file in the remote backend

**Warning!** It is highly recommended that you `enable Bucket Versioning` on the S3 bucket to allow for state recovery in the case of accidental deletions and human error.

**Note**: We will need this bucket name in the later step

### Create a Dynamo DB table for state file locking

- Give the table a name
- Make sure to add a `Partition key` with the name `LockID` and type as `String`

### Generate a public-private key pair for our instances

We need a public key and a private key for our server so please follow the procedure I've included below.

```sh
cd modules/key/
ssh-keygen
```

The above command asks for the key name and then gives `client_key` it will create a pair of keys one public and one private. you can give any name you want but then you need to edit the Terraform file

Edit the below file according to your configuration

```sh
vim root/backend.tf
```

Add the below code in root/backend.tf

```sh
terraform {
  backend "s3" {
    bucket = "BUCKET_NAME"
    key    = "backend/FILE_NAME_TO_STORE_STATE.tfstate"
    region = "us-east-1"
    dynamodb_table = "dynamoDB_TABLE_NAME"
  }
}
```

### 🏠 Let's set up the variable for our Infrastructure

Create one file with the name `terraform.tfvars`

```sh
vim root/terraform.tfvars
```

### 🔐 ACM certificate

Go to AWS console --> AWS Certificate Manager (ACM) and make sure you have a valid certificate in Issued status, if not, feel free to create one and use the domain name on which you are planning to host your application.

### 👨‍💻 Route 53 Hosted Zone

Go to AWS Console --> Route53 --> Hosted Zones and ensure you have a public hosted zone available, if not create one.

Add the below content into the `root/terraform.tfvars` file and add the values of each variable.

```javascript
region = "";
project_name = "";
vpc_cidr = "";
pub_sub_1a_cidr = "";
pub_sub_2b_cidr = "";
pri_sub_3a_cidr = "";
pri_sub_4b_cidr = "";
pri_sub_5a_cidr = "";
pri_sub_6b_cidr = "";
db_username = "";
db_password = "";
certificate_domain_name = "";
additional_domain_name = "";
```

## ✈️ Now we are ready to deploy our application on the cloud ⛅

get into the project directory

```sh
cd root
```

👉 let install dependency to deploy the application

```sh
terraform init
```

Type the below command to see the plan of the execution

```sh
terraform plan
```

✨Finally, HIT the below command to deploy the application...

```sh
terraform apply
```

Type `yes`, and it will prompt you for approval

After go to EC2 Load alancers, copy the link in a browser:


<img width="890" alt="Screenshot 2024-09-19 at 21 49 35" src="https://github.com/user-attachments/assets/111cd299-7d94-4e30-b80d-b544d17eadae">


and the app is up and running!

<img width="861" alt="Screenshot 2024-09-19 at 21 50 14" src="https://github.com/user-attachments/assets/84dbf1e0-e7fd-49f9-b1b6-8a4f93d402d5">

if you go to CloudFront and take the domain name, the website is also accessible over this domain:

<img width="728" alt="Screenshot 2024-09-19 at 21 51 12" src="https://github.com/user-attachments/assets/0129354e-19d7-477a-ae16-66ce462a9c51">

<img width="580" alt="Screenshot 2024-09-19 at 21 51 48" src="https://github.com/user-attachments/assets/d8ccef09-6267-411b-ac98-bd230ade6e38">

<img width="848" alt="Screenshot 2024-09-19 at 21 52 25" src="https://github.com/user-attachments/assets/237989e9-b2c3-4064-8900-ba311f82a0bd">
