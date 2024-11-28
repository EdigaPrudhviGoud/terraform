1.Configure the AWS CLI:
aws configure

You’ll need to input your:
    Access Key ID
    Secret Access Key
    Region
    Output format (typically json)


2.terraform --version

3.AWS IAM User or Role with Permissions

    You’ll need an AWS IAM User or Role with sufficient permissions to create and manage EC2 instances and associated resources (like VPC, subnets, security groups, etc.).
    The following IAM policy permissions are typically required:
        ec2:RunInstances
        ec2:DescribeInstances
        ec2:TerminateInstances
        ec2:DescribeSecurityGroups
        ec2:CreateSecurityGroup
        ec2:DescribeKeyPairs
        ec2:DescribeSubnets
        ec2:DescribeVpcs
        And others, depending on the complexity of the resources you're managing.

4.Security Key Pair for EC2
aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem

5.VPC and Subnet (optional)

    By default, Terraform will use the default VPC and subnet, but if you want more control, you might want to configure your own VPC, subnets, route tables, etc.
    You can specify the VPC and subnet when defining your EC2 instance, or create them using Terraform as part of your configuration.

6.Copy the below Terraform AWS Provider Configuration
provider "aws" {
  region  = "us-west-2" # specify your region
  access_key = "your-access-key-id"
  secret_key = "your-secret-access-key"
}

Alternatively, you can use environment variables to avoid hardcoding credentials:
export AWS_ACCESS_KEY_ID="your-access-key-id"
export AWS_SECRET_ACCESS_KEY="your-secret-access-key"

7. create .tf file

provider "aws" {
  region = "us-west-2"
}

resource "aws_instance" "my_ec2" {
  ami           = "ami-0c55b159cbfafe1f0"  # example AMI ID for Amazon Linux 2 (check region-specific AMIs)
  instance_type = "t2.micro"               # instance type
  key_name      = "MyKeyPair"              # reference to the key pair you created

  tags = {
    Name = "MyEC2Instance"
  }
}


terraform init (downloads necessary provider plugins and initializes your working directory)
terraform plan (shows you what changes Terraform will make)
terraform apply (creates the EC2 instance as per your configuration)
___________________________________________________________________________________________________________________________________________________

4.Create two ec2 in two regions in terraform


# Provider Configuration for Region 1 (e.g., us-east-1)
provider "aws" {
  alias  = "us_east_1"
  region = "us-east-1"
}

# Provider Configuration for Region 2 (e.g., us-west-2)
provider "aws" {
  alias  = "us_west_2"
  region = "us-west-2"
}

# EC2 Instance in us-east-1
resource "aws_instance" "us_east_instance" {
  provider = aws.us_east_1

  ami           = "ami-0c55b159cbfafe1f0"  # Update with a valid AMI ID for us-east-1 region
  instance_type = "t2.micro"
  tags = {
    Name = "us-east-1-instance"
  }
}

# EC2 Instance in us-west-2
resource "aws_instance" "us_west_instance" {
  provider = aws.us_west_2

  ami           = "ami-0c55b159cbfafe1f0"  # Update with a valid AMI ID for us-west-2 region
  instance_type = "t2.micro"
  tags = {
    Name = "us-west-2-instance"
  }
}
________________________________________________________________________________________________________________________________
To create two EC2 instances with two different users using Terraform

1.~/.aws/credentials
[default]
aws_access_key_id = YOUR_DEFAULT_USER_ACCESS_KEY
aws_secret_access_key = YOUR_DEFAULT_USER_SECRET_KEY

[user1]
aws_access_key_id = YOUR_USER1_ACCESS_KEY
aws_secret_access_key = YOUR_USER1_SECRET_KEY

[user2]
aws_access_key_id = YOUR_USER2_ACCESS_KEY
aws_secret_access_key = YOUR_USER2_SECRET_KEY


2.Terraform Configuration Using Profiles
# Provider Configuration for User 1 using the 'user1' profile
provider "aws" {
  alias   = "user1"
  region  = "us-east-1"
  profile = "user1"
}

# Provider Configuration for User 2 using the 'user2' profile
provider "aws" {
  alias   = "user2"
  region  = "us-west-2"
  profile = "user2"
}

# EC2 Instance created by User 1 (us-east-1)
resource "aws_instance" "user1_instance" {
  provider = aws.user1

  ami           = "ami-0c55b159cbfafe1f0"  # Update with a valid AMI ID for us-east-1 region
  instance_type = "t2.micro"
  tags = {
    Name = "user1-instance"
  }
}

# EC2 Instance created by User 2 (us-west-2)
resource "aws_instance" "user2_instance" {
  provider = aws.user2

  ami           = "ami-0c55b159cbfafe1f0"  # Update with a valid AMI ID for us-west-2 region
  instance_type = "t2.micro"
  tags = {
    Name = "user2-instance"
  }
}

