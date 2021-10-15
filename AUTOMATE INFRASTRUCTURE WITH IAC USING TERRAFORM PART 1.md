# AUTOMATE INFRASTRUCTURE WITH IAC USING TERRAFORM PART 1

![image](https://user-images.githubusercontent.com/22638955/137396922-1a2c1b9d-20b0-457d-ab6f-c5640093f602.png)

We would be automating the deploying of the above infrastructure using Terraform.

Create an IAM user, name it terraform (ensure that the user has only programatic access to your AWS account) and grant this user AdministratorAccess permissions.

![image](https://user-images.githubusercontent.com/22638955/137397312-085e0f1b-9b4b-4e35-9966-fbc6b1140baa.png)

Download the csv containing the `secret access key` and `access key ID`.

Configure programmatic access from your workstation to connect to AWS using the access keys saved above and a `Python SDK (boto3)`. You must have `Python 3.6` or higher on your workstation.

Do a Python installation first
```
sudo apt-get update
sudo apt-get install python3.6
```
Then install `pip` using this [link](https://www.tecmint.com/install-pip-in-linux/)

Install boto3 using pip with `pip install boto3`

For easier authentication configuration, let's use `AWS CLI` with `aws configure` command.
Install `AWS CLI` using the below commands -
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install
```
Run the `aws configure` command to input the previously saved `secret access key` and `access key id`
![image](https://user-images.githubusercontent.com/22638955/137400393-09771f8b-43a4-4ae2-ac4f-b7418d600df3.png)

![image](https://user-images.githubusercontent.com/22638955/137401380-7884da6d-bc21-4c9c-90b1-7a9b45b37e6a.png)

## **VPC | SUBNETS | SECURITY GROUPS**

We would be creating our directory structure using VSCode
* Create a folder called `PBL`
* Create a file in the `PBL` folder and name it `main.tf`
* Setup the Terraform CLI using the commands below -
```
$ sudo apt-get update && sudo apt-get install -y gnupg software-properties-common curl
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
$ sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
$ sudo apt-get update && sudo apt-get install terraform
$ terraform -help
```
![image](https://user-images.githubusercontent.com/22638955/137403609-aa1b69c9-5cf8-4da9-9f86-f65849280419.png)

No we are going to add in the below `HCL code` into our `main.tf` file
```
provider "aws" {
  region = "us-east-2"
}

# Create VPC
resource "aws_vpc" "main" {
  cidr_block                     = "172.16.0.0/16"
  enable_dns_support             = "true"
  enable_dns_hostnames           = "true"
  enable_classiclink             = "false"
  enable_classiclink_dns_support = "false"
}
```

From the above `HCL` code, we have `aws` as our provider, and we have specified `us-east-2` as the region we want to create the `vpc` in

We would now run `terraform init` to download plugins to be used by the providers and provisioners
![image](https://user-images.githubusercontent.com/22638955/137407417-f265abc3-d74a-484f-b43e-1a0eb2e33d56.png)

From the above image, we can see that a new directory has been created: `.terraform`. This is where Terraform keeps plugins. Generally, it is safe to delete this folder. It just means that you must execute terraform init again, to download them.

Run `terraform plan`
Then, if you are happy with changes planned, execute `terraform apply`

### Subnets resource section
According to our architectural design, we require 6 subnets:

* 2 public
* 2 private for webservers
* 2 private for data layer

Let us create the first 2 public subnets.

Add below configuration to the main.tf file:
```
# Create public subnets1
    resource "aws_subnet" "public1" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.0.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "us-east-2a"

}

# Create public subnet2
    resource "aws_subnet" "public2" {
    vpc_id                     = aws_vpc.main.id
    cidr_block                 = "172.16.1.0/24"
    map_public_ip_on_launch    = true
    availability_zone          = "us-east-2b"
}
```
From the above, we are creating 2 subnets, therefore declaring 2 resource blocks – one for each of the subnets.

Run `terraform plan` and `terraform apply`

## FIXING THE PROBLEMS BY CODE REFACTORING

Fixing Hard Coded Values: We will introduce variables, and remove hard coding.

We would declare variables in our `main.tf` file as shown below
```
variable "region" {
        default = "us-east-2"
    }

    variable "vpc_cidr" {
        default = "172.16.0.0/16"
    }

    variable "enable_dns_support" {
        default = "true"
    }

    variable "enable_dns_hostnames" {
        default ="true" 
    }

    variable "enable_classiclink" {
        default = "false"
    }

    variable "enable_classiclink_dns_support" {
        default = "false"
    }

    provider "aws" {
    region = var.region
    }

    # Create VPC
    resource "aws_vpc" "main" {
    cidr_block                     = var.vpc_cidr
    enable_dns_support             = var.enable_dns_support 
    enable_dns_hostnames           = var.enable_dns_support
    enable_classiclink             = var.enable_classiclink
    enable_classiclink_dns_support = var.enable_classiclink

    }
```

Fixing multiple resource blocks
Let us fetch Availability zones from AWS using terraforms data sources, and replace the hard coded value in the subnet’s availability_zone section.





![image](https://user-images.githubusercontent.com/22638955/137412683-0d3788b1-a39d-4762-b9ee-395e31c6eb9e.png)

![image](https://user-images.githubusercontent.com/22638955/137412774-ef17172c-0559-451f-814e-8ea2bd70e58e.png)

