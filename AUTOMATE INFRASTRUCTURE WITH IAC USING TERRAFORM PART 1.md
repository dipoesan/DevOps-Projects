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

